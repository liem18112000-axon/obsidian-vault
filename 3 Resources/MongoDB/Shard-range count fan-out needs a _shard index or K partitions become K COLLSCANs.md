---
title: "Shard-range count fan-out needs a _shard index or K partitions become K COLLSCANs"
created: 2026-06-26
type: lesson
status: seedling
source: "session 2026-06-26 LUZ-154613 parallelize review"
tags: [mongodb, luz-docs, fanout, sharding, performance, gotcha]
---

# Shard-range count fan-out needs a _shard index or K partitions become K COLLSCANs

A `_shard`-range count fan-out (split one count into K disjoint `_shard` ranges, run concurrently, sum) only speeds up the count if there is an **index on `_shard`**. Each sub-count filters `_shard >= lo AND _shard < hi`; with no index, every partition resolves by COLLSCAN, so K partitions = **K concurrent full scans** over the same collection contending for one cache = slower than a single count, not faster. The technique inverts without the index.

## Context
Found reviewing luz-docs `parallelize` package (branch `kepler/sprint-159/LUZ-154613-shard-adapt-migraiton`, `ParallelizePartitioner` / `ParallelizeCount`). SHARD_SPACE = 2^30, K clamped to 64, config `luz.docs.parallelize.count-partitions` (default 1 = off). The DESIGN.md did **not** mention creating a `_shard` index — the #1 thing to verify before claiming speedup. Best index = compound leading with the baseQuery match fields then `_shard`, so each partition gets a bounded IXSCAN.

## Two related gotchas in the same package
- The sharding-complete gate probes `{_shard:{$exists:false}}`, which is **non-indexable** ($exists:false is not served by normal or sparse indexes) → a COLLSCAN on every cache miss (every 60s while migration incomplete). Cheaper: read completeness from the migration Campaign status.
- Fan-out parallelizes work, it does not reduce it: ~K× wall-clock ONLY while data is cache-resident (CPU-bound); past the disk-bound wall, K concurrent scans blow cache and K stops helping. See [[Mongo facet $group count index only helps the $match prefix, not the count]].

## Scope
This fan-out is count-only (`ToIntFunction<JsonObject>` → int). It cannot carry facet buckets ($group/$sum:1 returns an array); facet parallelization needs a per-bucket-merge variant.

## Related
[[Mongo facet $group count index only helps the $match prefix, not the count]]

## Related

- [[Mongo facet $group count: index only helps the $match prefix]]
- [[not the count]]
