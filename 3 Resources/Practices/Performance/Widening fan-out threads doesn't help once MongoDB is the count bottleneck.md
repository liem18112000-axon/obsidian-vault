---
title: "Widening fan-out threads doesn't help once MongoDB is the count bottleneck"
created: 2026-06-18
type: observation
status: seedling
source: "luz_docs LUZ-154613 2026-06-18"
tags: [performance, mongodb, concurrency, benchmarking, negative-result]
---

# Widening fan-out threads doesn't help once MongoDB is the count bottleneck

Negative result: widening the client-side fan-out from a 6-thread pool to a 64-thread pool did NOT lower a scan-bound MongoDB count. On a non-sharded replica set, K=24 and K=64 sub-counts (64-thread WildFly executor) both floored at ~3.0s — the SAME as K=11/12 on the default 6-thread pool.

Why: the K parallel sub-counts all hit the same replica-set primary and contend on its CPU/IO/WiredTiger read tickets. Beyond ~6 concurrent partial-scans they just time-slice the same cores. So MongoDB does not parallelize THIS scan past ~6-wide; more app threads only burn more CPU (K=24 burst ~1 core, higher at 32/64) for zero latency gain.

Lesson: when a parallel-count fan-out plateaus, first prove WHERE the cap is before adding threads/pods. Here the cap was MongoDB, not the app thread pool — so the obvious "raise the pool to 32/64" lever was a dead end. The ceiling moved from luz-docs (6 default threads) to Mongo (~6 effective parallel scans), and the wall-clock floor (~3s) did not move.

Practical: keep the fan-out at K~=6 (sits on the floor with least CPU/downstream load); reach sub-2s only by removing the scan (index-covered COUNT_SCAN, Roaring bitmap) or caching — not by more parallelism. To genuinely add scan parallelism you would need more Mongo nodes (secondary reads) or a sharded cluster, not more app threads.

Related: [[Shard count fan-out: most of the win is at K=4, diminishing returns after]] · [[BitmapHLL counts supersede fan-out; they don't combine with it]] · [[WildFly custom managed-executor-service needs context-service for CDIWeld tasks]]

## Related

- [[Shard count fan-out: most of the win is at K=4]]
- [[diminishing returns after]]
- [[WildFly custom managed-executor-service needs context-service for CDIWeld tasks]]
