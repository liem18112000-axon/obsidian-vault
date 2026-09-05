---
title: "Trust median and p90 over many runs, not the mean of a few, for latency on a noisy shared env"
created: 2026-08-13
type: technique
status: seedling
source: "luz-docs-import perf benchmark 2026-08-13"
tags: [benchmarking, latency, percentiles, statistics, performance]
---

# Trust median and p90 over many runs, not the mean of a few, for latency on a noisy shared env

When benchmarking latency on a **noisy / shared environment**, a handful of runs can't separate signal from load spikes, and the **mean is not robust** — one outlier dominates it. Prefer **many samples (e.g. N=100) and report the median + p90/p95**, not the mean of a few.

Concrete example (250-doc import ×100 on a shared perf cluster): min 23.1 s, **median 33.6 s**, p90 50.1 s, p95 59.7 s, mean 44.8 s, **max 935 s** (one load spike ~28× the median). The single 935 s outlier drags the *mean* to 44.8 but barely moves p90/p95 — so median/p90 describe the real steady-state while the mean would mislead. With only 3–5 runs that same spike would look like "the result".

**Rule of thumb:** on shared infra, sample size buys you robustness. Report percentiles; call out the max as an outlier; don't quote the mean as "the" number.

Related: [[Small import batches are overhead-bound so their per-item throughput is lower than large batches]], [[A latency penalty in the tail not the median points to JIT/GC warm-up under CPU contention]].

## Related

- [[Small import batches are overhead-bound so their per-item throughput is lower than large batches]]
- [[A latency penalty in the tail not the median points to JIT/GC warm-up under CPU contention]]
