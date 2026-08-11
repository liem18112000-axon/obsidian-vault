---
ai_hash: 36932933cadfdef4
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-16
entities:
- Fan-out count
- key-absent sub-count
- shard backfill
- count
- fan-out partitioning
- _shard field
- documents
- range sub-counts
- exists:false bucket
- luz-docs
- LUZ-154613
- migration
- _countShard field
- lazily-populated key
- explicit 'key-absent' partition
- K+1 buckets
- sum
- field RENAME
- Partition the materialized count on a uniform _shard int
- not _id
source: LUZ-154613 session 2026-06-16
status: seedling
tags:
- luz-docs
- materialize
- mongodb
- sharding
- backfill
- gotcha
title: Fan-out count needs an explicit key-absent sub-count to stay exact during shard
  backfill
type: lesson
---

# Fan-out count needs an explicit key-absent sub-count to stay exact during shard backfill

When a count is fan-out-partitioned on a stored shard field (_shard) that is backfilled gradually, docs not yet stamped match NO range and would be undercounted during rollout. Fix: add one extra sub-count for {_shard:{$exists:false}} alongside the K range sub-counts. The K+1 buckets are disjoint (a doc either has _shard → exactly one range, or lacks it → the exists:false bucket) and cover every doc, so the sum stays EXACT throughout backfill — no waiting for 100% stamped before enabling fan-out.

Proven on luz-docs LUZ-154613 (tenant 128k docs, none stamped yet): the 4 range sub-counts returned 0, the exists:false sub-count returned 128000, sum = 128000. As the migration stamps _shard, docs move from the exists:false bucket into the int ranges; total never drifts.

Bonus: this also made the count robust to a field RENAME (_countShard → _shard orphaned the old field on existing docs; the exists:false bucket counted them correctly until the migration re-stamped _shard). General principle: when partitioning on a lazily-populated key, always include an explicit 'key-absent' partition.

## Related

- [[Partition the materialized count on a uniform _shard int]]
- [[not _id]]

%% ai-graph-start %%

**Related notes:**
- [[luz-docs parallelized count undercounts documents missing _shard]]
- [[No existing luz-docs field works as a fan-out count partition key — survey]]
- [[Fan-out gate and backfill filter must cover the same field set]]
- [[Random shard key gives balanced fan-out partitions (equal-width = equal-work only if uniform)]]
- [[Don't share one predicate between a read-path gate and a backfill selector]]

**Relations:**
- Fan-out count — *needs* — key-absent sub-count
- Fan-out count — *stays exact during* — shard backfill
- count — *is fan-out-partitioned on* — _shard field
- _shard field — *is backfilled during* — shard backfill
- documents — *are undercounted without* — key-absent sub-count
- key-absent sub-count — *represents condition* — {_shard:{$exists:false}}
- key-absent sub-count — *is alongside* — range sub-counts
- exists:false bucket — *is a type of* — key-absent sub-count
- K+1 buckets — *comprise* — exists:false bucket
- K+1 buckets — *comprise* — range sub-counts
- K+1 buckets — *are* — disjoint
- K+1 buckets — *cover* — documents
- sum — *of K+1 buckets stays exact during* — shard backfill
- solution — *proven on* — luz-docs
- luz-docs — *has issue* — LUZ-154613
- LUZ-154613 — *involves* — documents
- migration — *stamps* — _shard field
- documents — *move from* — exists:false bucket
- documents — *move to* — range sub-counts
- key-absent sub-count — *provides robustness for* — field RENAME
- field RENAME — *changes* — _countShard field
- _countShard field — *to* — _shard field
- lazily-populated key — *requires* — explicit 'key-absent' partition
- Fan-out count — *is related to* — Partition the materialized count on a uniform _shard int
- Fan-out count — *is related to* — not _id

%% ai-graph-end %%