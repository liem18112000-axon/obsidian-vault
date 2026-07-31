---
title: "luz_docs ParallelizeGate needs two indexes on _shard"
created: 2026-07-13
type: lesson
status: seedling
source: "luz_docs session 2026-07-13"
tags: [luz-docs, mongodb, parallelize, performance, indexing]
---

# luz_docs ParallelizeGate needs two indexes on _shard

luz_docs's `ch.klara.luz.docs.parallelize` package has **no index on `_shard` at all**. The investigation (and the report) ended up scoped to just one query shape: `ParallelizeQueryBuilder.missingField(SHARD)` → `{ _shard: { $exists: false } }`, issued by both `ParallelizeGate.isShardingComplete()` (the cached gate check) and `ParallelizeMigrationExecutor.execute()` (backfill paging). Since it's unindexed, every call is a full `COLLSCAN`, so cost scales O(N) with collection size — the reported roughly-linear slowdown between 128k and 1M+ docs per tenant. `ParallelizeGate` re-runs this on every cache miss (60s while incomplete, 3600s once complete) for the tenant's whole lifetime, so it isn't a one-off cost.

(`ParallelizeCount`'s separate `{$gte,$lt}` range-based fan-out queries against `_shard` would need their own, non-partial full index — a different concern, deliberately dropped from this investigation's scope to keep the report focused on the one fix that matters here.)

Fix: a **partial index** matching the query exactly —

```js
db.documents.createIndex(
  { _shard: 1 },
  { partialFilterExpression: { _shard: { $exists: false } } }
)
```

See [[MongoDB partial index shrinks with a completing backfill]] for why this turns an O(N) `COLLSCAN` into an O(M) `IXSCAN` (M = docs still missing `_shard`) that shrinks toward empty as a tenant's backfill campaign completes — and for the byte-level breakdown of why such an index is cheap to carry even at 1M-doc scale.

No compound key needed — the query doesn't combine `_shard` with another filter that Mongo could fold into the same index.

Full writeup + before/after Excalidraw diagram: `docs/count-chunk/shard-index-optimization-report.md` in luz_docs. Related: [[Shard gate strict + write-path gap]] (the earlier finding that the write path didn't stamp `_shard` on create, causing the gate to flip-flop).

## Related

- [[MongoDB partial index shrinks with a completing backfill]]
- [[Shard gate strict + write-path gap]]
