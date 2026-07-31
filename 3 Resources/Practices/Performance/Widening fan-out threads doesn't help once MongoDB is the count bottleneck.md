---
title: "Widening fan-out threads doesn't help once MongoDB is the count bottleneck"
created: 2026-06-18
type: observation
status: seedling
source: "luz_docs LUZ-154613 dev sweeps 2026-06-18"
tags: [performance, mongodb, concurrency, scaling, benchmarking, counting, negative-result]
---

# Widening fan-out threads doesn't help once MongoDB is the count bottleneck

Negative result: widening the client-side fan-out from a 6-thread pool to a 64-thread pool did NOT lower a scan-bound MongoDB count. On a non-sharded replica set, K=24 and K=64 sub-counts (64-thread WildFly executor) both floored at ~3.0 s — the SAME as K=11/12 on the default 6-thread pool.

Why: the K parallel sub-counts all hit the same replica-set primary and contend on its CPU/IO/WiredTiger read tickets. Past ~6 concurrent partial scans they just time-slice the same cores. More app threads only burn more CPU (K=24 burst ~1 core, higher at 32/64) for zero latency gain. Corollary: the fan-out **speedup ratio decays toward 1x as the dataset grows** — Mongo saturates earlier relative to the work, so the same K buys less and less.

Lesson: when a parallel-count fan-out plateaus, prove WHERE the cap is before adding threads or pods. Here the ceiling moved from luz-docs (6 default threads) to Mongo (~6 effective parallel scans) and the wall-clock floor (~3 s) did not move, so the obvious "raise the pool to 32/64" lever was a dead end.

Practical: keep the fan-out at K≈6 (on the floor, least CPU/downstream load); reach sub-2 s only by removing the scan (index-covered COUNT_SCAN, Roaring bitmap) or caching — not by more parallelism. Real scan parallelism requires more Mongo nodes (secondary reads) or a sharded cluster, not more app threads.

## Related

- [[Shard count fan-out most of the win is at K=4, diminishing returns after]]
- [[BitmapHLL counts supersede fan-out; they don't combine with it]]
- [[WildFly custom managed-executor-service needs context-service for CDIWeld tasks]]
