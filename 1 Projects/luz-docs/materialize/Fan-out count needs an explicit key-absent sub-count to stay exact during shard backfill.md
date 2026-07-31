---
title: "Fan-out count needs an explicit key-absent sub-count to stay exact during shard backfill"
created: 2026-06-16
type: lesson
status: seedling
source: "LUZ-154613 session 2026-06-16"
tags: [luz-docs, materialize, mongodb, sharding, backfill, gotcha]
---

# Fan-out count needs an explicit key-absent sub-count to stay exact during shard backfill

When a count is fan-out-partitioned on a stored shard field (_shard) that is backfilled gradually, docs not yet stamped match NO range and would be undercounted during rollout. Fix: add one extra sub-count for {_shard:{$exists:false}} alongside the K range sub-counts. The K+1 buckets are disjoint (a doc either has _shard → exactly one range, or lacks it → the exists:false bucket) and cover every doc, so the sum stays EXACT throughout backfill — no waiting for 100% stamped before enabling fan-out.

Proven on luz-docs LUZ-154613 (tenant 128k docs, none stamped yet): the 4 range sub-counts returned 0, the exists:false sub-count returned 128000, sum = 128000. As the migration stamps _shard, docs move from the exists:false bucket into the int ranges; total never drifts.

Bonus: this also made the count robust to a field RENAME (_countShard → _shard orphaned the old field on existing docs; the exists:false bucket counted them correctly until the migration re-stamped _shard). General principle: when partitioning on a lazily-populated key, always include an explicit 'key-absent' partition.

## Related

- [[Partition the materialized count on a uniform _shard int]]
- [[not _id]]
