---
title: "luz-docs-import performance-env import benchmark findings"
created: 2026-08-13
type: observation
status: seedling
source: "docs/tests/import-benchmark-2026-08-13_env_performance* 2026-08-13"
tags: [luz-docs-import, performance, benchmark, import]
---

# luz-docs-import performance-env import benchmark findings

Benchmark of luz-docs-import ZIP import on the **performance** env (same code/image `c6b1e23` as dev, tenant `45b05710…`, clean-before-each-run), 2026-08-13. Reports in `docs/tests/import-benchmark-2026-08-13_env_performance*`.

Findings:
- **Performance ≈ 2× faster than dev** on identical fixtures — 1k warm ~46 s (dev ~100 s), 2.5k warm ~89 s (dev ~170 s); warm throughput ~22 / ~28 docs/s vs dev's ~10 / ~15. Milder cold-start (2.5k cold/warm 1.25× vs dev 2.4×) and **0 antivirus-metadata timeouts** (dev's 2.5k cold hit 3) — more downstream/AV headroom, a dedicated per-tenant Mongo shard, and 2 replicas.
- **250-doc × 100 runs:** 100/100 DONE, all 250 imported, 0 failures — median **33.6 s**, p90 50 s, one 935 s load-spike outlier. Reliable at N=100.
- **5k is high-variance on this shared env** — the same 5k import ranged 196 → 1077 s across the day purely from other workloads (uploads 5 → 26 s). Large-batch timings here measure cluster load, not code; use perf for reliability/soak, or many small samples so spikes average out.

Related: [[Performance Mongo is a mongos-routed sharded cluster — truncate via the tenant's shard primary, not mongos]], [[Trust median and p90 over many runs, not the mean of a few, for latency on a noisy shared env]], [[Small import batches are overhead-bound so their per-item throughput is lower than large batches]], [[luz-docs-import cold first-import slowness is JIT plus downstream re-warm on a CPU-limited pod]].

## Related

- [[Performance Mongo is a mongos-routed sharded cluster — truncate via the tenant's shard primary]]
- [[not mongos]]
- [[Trust median and p90 over many runs]]
- [[not the mean of a few]]
- [[for latency on a noisy shared env]]
- [[Small import batches are overhead-bound so their per-item throughput is lower than large batches]]
- [[luz-docs-import cold first-import slowness is JIT plus downstream re-warm on a CPU-limited pod]]
