---
ai_hash: 4b5187e76a23caee
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-13
entities:
- luz_docs
- ParallelizeGate
- _shard
- ch.klara.luz.docs.parallelize
- ParallelizeQueryBuilder.missingField(SHARD)
- ParallelizeGate.isShardingComplete()
- ParallelizeMigrationExecutor.execute()
- COLLSCAN
- O(N)
- ParallelizeCount
- partial index
- MongoDB
- IXSCAN
- O(M)
- MongoDB partial index shrinks with a completing backfill
- Shard gate strict + write-path gap
- docs/count-chunk/shard-index-optimization-report.md
- index
- query
- tenant
- backfill
- write path
- collection size
source: luz_docs session 2026-07-13
status: seedling
tags:
- luz-docs
- mongodb
- parallelize
- performance
- indexing
title: luz_docs ParallelizeGate needs two indexes on _shard
type: lesson
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

%% ai-graph-start %%

**Related notes:**
- [[MongoDB partial index shrinks with a completing backfill]]
- [[luz-docs parallelized count undercounts documents missing _shard]]
- [[luz_docs stamps _shard on create to keep sharding gate stable]]
- [[luz_jsonstore silently drops _shard on $set updates (HTTP 200, no persist)]]
- [[Count fan-out _shard index must put _shard LAST in the compound key (ESR)]]

**Relations:**
- luz_docs — *CONTAINS_PACKAGE* — ch.klara.luz.docs.parallelize
- ch.klara.luz.docs.parallelize — *CONTAINS_CLASS* — ParallelizeGate
- ParallelizeGate — *REQUIRES* — index
- index — *ON_FIELD* — _shard
- _shard — *LACKS* — index
- ParallelizeGate.isShardingComplete() — *ISSUES* — ParallelizeQueryBuilder.missingField(SHARD)
- ParallelizeMigrationExecutor.execute() — *ISSUES* — ParallelizeQueryBuilder.missingField(SHARD)
- ParallelizeQueryBuilder.missingField(SHARD) — *TARGETS_FIELD* — _shard
- ParallelizeQueryBuilder.missingField(SHARD) — *IS_A* — query
- query — *WITHOUT_INDEX_CAUSES* — COLLSCAN
- COLLSCAN — *SCALES_AS* — O(N)
- O(N) — *DEPENDS_ON* — collection size
- ParallelizeGate — *RE_RUNS* — query
- ParallelizeCount — *QUERIES* — _shard
- partial index — *IS_A* — Fix
- partial index — *APPLIES_TO* — _shard
- partial index — *OPTIMIZES* — query
- partial index — *HAS_FILTER_EXPRESSION* — { _shard: { $exists: false } }
- partial index — *CONVERTS* — COLLSCAN
- COLLSCAN — *TO* — IXSCAN
- IXSCAN — *SCALES_AS* — O(M)
- MongoDB partial index shrinks with a completing backfill — *EXPLAINS* — partial index
- docs/count-chunk/shard-index-optimization-report.md — *IS_A* — Full writeup
- docs/count-chunk/shard-index-optimization-report.md — *IS_LOCATED_IN* — luz_docs
- Shard gate strict + write-path gap — *IS_RELATED_TO* — ParallelizeGate
- write path — *FAILED_TO_STAMP* — _shard
- backfill — *COMPLETES_FOR* — tenant
- MongoDB — *SUPPORTS* — partial index

%% ai-graph-end %%