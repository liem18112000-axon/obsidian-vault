---
title: "MongoDB partial index shrinks with a completing backfill"
created: 2026-07-13
type: lesson
status: seedling
source: "luz_docs session 2026-07-13"
tags: [mongodb, partial-index, backfill, performance]
---

# MongoDB partial index shrinks with a completing backfill

A MongoDB query filtering on `{ field: { $exists: false } }` (the "not yet migrated/stamped" check during a backfill) can use a **partial index** whose `partialFilterExpression` matches that exact predicate:

```js
db.collection.createIndex(
  { field: 1 },
  { partialFilterExpression: { field: { $exists: false } } }
)
```

Because the index only stores entries for documents matching the partial filter, it physically shrinks as the backfill/migration completes — the recheck query goes from an O(N) `COLLSCAN` (cost = whole collection size, forever) to an O(M) `IXSCAN` where M = docs still missing the field. Once the backfill finishes, M → 0 and the check stays cheap permanently, not just until some cache TTL expires.

This does NOT help if the same field is also queried with a range (e.g. `{field: {$gte:a, $lt:b}}` for bucketed/sharded fan-out) — a partial index scoped to "missing" values has no entries for the populated range, so a second, full (non-sparse, non-partial) index on the same field is needed for that job. One field can legitimately need two indexes when two different query shapes are used against it for two different purposes (a targeted completeness check vs. a full-range partition scan).

Found while investigating a linear-with-N slowdown in luz_docs's [[luz_docs ParallelizeGate needs two indexes on _shard]].

**Sizing a partial index:** a single-field index entry on a small scalar (e.g. an int32) runs roughly ~20-40 bytes per entry in practice (type/value encoding + RecordId + WiredTiger B-tree overhead — confirm on real data via `db.collection.stats().indexSizes`). Because a partial index's size tracks M (matching docs), not N (total docs), its footprint is worst-case ~20-40 MB per million matching docs *only while the backfill is incomplete*, then shrinks toward a few KB once the backfill finishes — versus a non-partial index on the same field, which sits at that ~20-40 MB permanently regardless of backfill progress.

**Why ~20-40 bytes/entry, broken down:** a WiredTiger index entry = KeyString-encoded key + RecordId + B-tree cell overhead, not just "the value's byte size."
1. **Key bytes** — for an `{$exists:false}` partial index specifically, there's no value to encode (the field is absent), so KeyString stores a single fixed "missing" marker: **~1 byte**, and it's *identical for every entry* — about as cheap and compressible as an index key can be.
2. **RecordId** — variable-length encoded pointer back to the document; **~3-4 bytes** for collections up to a few million docs.
3. **Page/cell overhead** — each entry sits in a WiredTiger leaf-page cell with its own small header; **~3-6 bytes**, before page-fill inefficiency.

Floor ≈ `1 + 4 + 6 ≈ 11 bytes/entry`. The commonly-quoted ~20-40 bytes/entry range is a deliberately conservative planning estimate above that floor (accounting for page-fill inefficiency and B-tree internal-node overhead not fully amortized) — real figures for an `{$exists:false}` partial index specifically (all-identical keys) plausibly land at or below the low end, since WiredTiger has maximum opportunity to compress a run of identical keys.

## Related

- [[luz_docs ParallelizeGate needs two indexes on _shard]]
