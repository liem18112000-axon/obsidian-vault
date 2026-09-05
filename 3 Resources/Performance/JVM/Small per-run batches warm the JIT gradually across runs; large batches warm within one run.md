---
title: "Small per-run batches warm the JIT gradually across runs; large batches warm within one run"
created: 2026-08-13
type: technique
status: seedling
source: "luz-docs-import run-1 cold-start investigation 2026-08-13"
tags: [jvm, jit, hotspot, performance, benchmarking]
---

# Small per-run batches warm the JIT gradually across runs; large batches warm within one run

HotSpot compiles a method to optimized native code only after it has been invoked ~N times (tiered-compilation thresholds). The count of warm-up invocations is therefore roughly **fixed**, independent of how you batch the work — which gives a useful discriminator when attributing run-to-run timing variance:

- A **small per-run workload** (e.g. 1000 items) may not reach the compile threshold in a single run, so it keeps **speeding up gradually across several runs** — each run finishes warmer than the last.
- A **large per-run workload** (e.g. 5000 items) crosses the threshold **within run 1**, so run 1 is a big outlier and runs 2+ are flat.

**Rule of thumb:** if "the small case decreases monotonically over N runs while the large case is run-1-outlier-then-flat", the cause is **JIT warm-up**. A fixed-cost connection/pool warmup would instead make even the small case flat after run 1, so that pattern rules pooling out.

Related: [[A latency penalty in the tail not the median points to JIT/GC warm-up under CPU contention]], [[Truncating DB collections between benchmark runs resets data but not service warmth]].

## Related

- [[A latency penalty in the tail not the median points to JIT/GC warm-up under CPU contention]]
- [[Truncating DB collections between benchmark runs resets data but not service warmth]]
