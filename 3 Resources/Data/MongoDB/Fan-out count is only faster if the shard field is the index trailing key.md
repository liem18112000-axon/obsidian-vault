---
title: "Fan-out count is only faster if the shard field is the index trailing key"
created: 2026-06-17
type: lesson
status: seedling
source: "luz_docs LUZ-154613 review 2026-06-17"
tags: [mongodb, performance, indexing, code-review]
---

# Fan-out count is only faster if the shard field is the index trailing key

Splitting one `count(query)` into K parallel sub-counts over disjoint ranges of a field (a random `_shard` int, or any bucket key) is only faster if a **compound index has that field as its TRAILING key**, after the base-query predicate fields.

Reason: MongoDB uses **one index per count**. If the shard field is not in the index that serves the base query, each of the K sub-counts (`{...base, _shard:{$gte,$lt}}`) still scans the full base-query result set and post-filters by `_shard` — so K sub-counts do ~K× the work of the single count they replaced. A stand-alone `{_shard:1}` index does not help either, because the base predicate must be satisfied by the same index.

So the fan-out is a **pessimization until** the count index is extended, e.g. `{_isPublic:1, _effectiveSecurityClassCodes:1, _shard:1}` (shard last). Caveat: a range/sort field before `_shard` in the key can still prevent a pure seek on the shard range.

Correctness of the fan-out is independent of the index: the K ranges plus a `{_shard:{$exists:false}}` bucket tile every doc disjointly, so the summed count is exact even mid-backfill. Only the *speed* depends on the index.

Practical check before enabling such a feature: (1) is the index in place with the bucket field trailing? (2) have all documents actually been stamped with the bucket field (else they all fall into the one $exists:false bucket and you get zero parallelism)?

Related: [[Don't share one predicate between a read-path gate and a backfill selector]]

## Related

- [[Don't share one predicate between a read-path gate and a backfill selector]]
