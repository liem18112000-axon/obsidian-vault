---
ai_hash: 0db79b2ea9a2268e
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-18
entities: []
source: luz_docs LUZ-154613 dev sweeps 2026-06-18
status: seedling
tags:
- performance
- mongodb
- concurrency
- scaling
- benchmarking
- counting
- negative-result
title: Widening fan-out threads doesn't help once MongoDB is the count bottleneck
type: observation
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

%% ai-graph-start %%

**Related notes:**
- [[Shard count fan-out most of the win is at K=4, diminishing returns after]]
- [[Dev benchmark _shard count fan-out ~1.8x, diminishing past K=12; local port-forward hid the gain]]
- [[Production security count is already COUNT_SCAN (covered); benchmark query's FETCH is inherent (multikey+$or+$nin)]]
- [[MongoDB $facet buckets add no parallelism and defeat COUNT_SCAN]]
- [[BitmapHLL counts supersede fan-out; they don't combine with it]]

%% ai-graph-end %%