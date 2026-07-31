---
title: "luz_docs /documents/count is scan-bound and cannot reach sub-second at 128k"
created: 2026-07-09
type: observation
status: seedling
source: "docs/perf-LUZ-154613-count-fanout-EXEC-SUMMARY.md and perf-LUZ-154613-count-scaling-findings-and-solution.md, referenced 2026-07-09"
tags: [luz-docs, kepler, mongodb, performance, count-optimization]
---

# luz_docs /documents/count is scan-bound and cannot reach sub-second at 128k

Real benchmark on a 128k-document luz_docs tenant, `POST /documents/count` (materialized read path): a single count (K=1) takes 14.1s median; the existing `_shard`-range fan-out at K=6 brings it to 3.15s median. Both are far over a hypothetical 500ms target for a live count.

The fan-out floor (~3s regardless of K) is caused by MongoDB primary contention — all K sub-counts run concurrently against the *same* replica-set primary on a non-sharded cluster, so they serialize against each other once the primary's capacity is saturated. Confirmed by widening the executor thread pool to 64 threads, which did not beat the K=6 floor: the bottleneck is Mongo, not app-level parallelism. Separately, the fan-out speedup itself decays with data size (4.5x at 128k, ~1x by 480k) because the *scan* is never removed, only spread across cores.

Implication: for a sub-second or sub-500ms count, scanning the matching set — in serial or in parallel — is a dead end. The only way to hit that target is to stop scanning per-request entirely: an epoch-keyed result cache, an index-covered COUNT_SCAN (materializing the predicate into an indexed boolean sentinel so `count()` reads index keys with no document FETCH), or a maintained cardinality structure (exact counter, Roaring bitmap, or approximate HyperLogLog sketch — see [[luz_docs count>N badge can use HyperLogLog with a fuzzy-zone fallback]]).

## Related

- [[luz_docs count>N badge can use HyperLogLog with a fuzzy-zone fallback]]
