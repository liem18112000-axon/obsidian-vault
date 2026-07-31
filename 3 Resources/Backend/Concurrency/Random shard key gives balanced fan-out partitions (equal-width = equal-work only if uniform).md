---
title: "Random shard key gives balanced fan-out partitions (equal-width = equal-work only if uniform)"
created: 2026-06-28
type: lesson
status: seedling
source: "session 2026-06-28 count-fanout deck"
tags: [parallelism, sharding, load-balancing, mongodb, luz-docs]
---

# Random shard key gives balanced fan-out partitions (equal-width = equal-work only if uniform)

When you parallelize a count (or any range scan) by cutting a key space into K equal-width ranges and running one sub-task per range, the sub-tasks only finish together if the key is **uniformly distributed**. Equal-WIDTH ranges give equal-WORK ranges only under uniformity.

If you partition on a real-world attribute — an ObjectId/_id, a creation timestamp, an auto-increment — values **cluster** (docs arrive in bursts; ids are time-ordered). One range then holds most of the documents, becomes a **straggler**, and fan-out is only as fast as that fullest bucket. The other K-1 workers finish early and idle → little or no speedup despite the parallelism.

The fix used in luz-docs count fan-out: stamp each document with a **uniformly random** `_shard` value (`ThreadLocalRandom.nextInt(2^30)`) and partition on that. Because it's uniform, every equal-width range holds ~N/K docs **by construction**, independent of insertion order, security codes, or folder shape. Balanced buckets → all sub-counts finish together → the full speedup. The randomness is what makes the fan-out worth doing.

Note: this is about balancing WORK (performance). Correctness of the summed count comes separately from the ranges tiling the whole space with no gap/overlap — that holds for any key. Random shard = balanced; tiling = exact. Two independent properties.

Related: [[concept-to-video avatar overlay sits bottom-right — keep callouts clear]] (same project's deck).
