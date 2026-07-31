---
title: "MongoDB partial index shrinks with a completing backfill"
created: 2026-07-13
type: lesson
status: seedling
source: "luz_docs session 2026-07-13"
tags: [mongodb, partial-index, backfill, performance]
---

# MongoDB partial index shrinks with a completing backfill

A backfill's "not yet stamped" recheck — `{field: {$exists: false}}` — is normally a permanent O(N) `COLLSCAN` (plain and sparse indexes cannot serve `$exists:false`). A **partial index whose `partialFilterExpression` is that exact predicate** can:

```js
db.collection.createIndex(
  { field: 1 },
  { partialFilterExpression: { field: { $exists: false } } }
)
```

It stores entries only for documents still missing the field, so the check becomes an O(M) `IXSCAN` and the index **physically shrinks as the backfill completes**. At M → 0 the check stays cheap permanently — not just until a cache TTL expires.

**It does not cover a range query on the same field.** A partial index scoped to "missing" holds no entries for populated values, so bucketed/sharded fan-out (`{field: {$gte:a, $lt:b}}`) needs a **second, full** index. One field legitimately needs two indexes when two query shapes serve two purposes: targeted completeness check vs. full-range partition scan.

**Sizing:** budget ~20-40 bytes per entry (KeyString key + RecordId + WiredTiger cell overhead); for an `$exists:false` index the key is a single identical "missing" marker, so the true floor is ~11 bytes and highly compressible. Footprint tracks M, not N — worst case ~20-40 MB per million still-unstamped docs, decaying to a few KB, versus a non-partial index that sits at that size forever. Confirm on real data with `db.collection.stats().indexSizes`.

Found while investigating a linear-with-N slowdown in [[luz_docs ParallelizeGate needs two indexes on _shard]].

## Related

- [[luz_docs ParallelizeGate needs two indexes on _shard]]
- [[Count fan-out _shard index must put _shard LAST in the compound key (ESR)]]
