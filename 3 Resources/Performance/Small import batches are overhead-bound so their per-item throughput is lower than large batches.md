---
title: "Small import batches are overhead-bound so their per-item throughput is lower than large batches"
created: 2026-08-13
type: concept
status: seedling
source: "luz-docs-import perf benchmark 2026-08-13"
tags: [performance, throughput, batch, overhead]
---

# Small import batches are overhead-bound so their per-item throughput is lower than large batches

A batch job has **fixed per-batch overhead** (setup, teardown, one-time fetches) plus **per-item work**. For a **small batch that overhead dominates**, so the effective per-item throughput is *lower* than for a large batch that amortizes the same overhead over more items. Counter-intuitively, "smaller = faster per item" is false here.

Measured on luz-docs-import ZIP import (perf env, median end-to-end): **250 docs ≈ 7.4 docs/s**, but **1k ≈ 22 docs/s** and **2.5k ≈ 28 docs/s**. The per-import fixed costs — unzip, a full folder pre-fetch (`getAllFoldersNoLimit`), seeding the unprocessed set, throttled job flushes, the final notification — are a big fraction of a 250-doc job but a rounding error on a 2.5k one. Bigger batches also warm the JIT within a single run (see the cold-start note).

**Takeaway:** when sizing/benchmarking batch work, expect throughput to *rise* with batch size until the per-item cost dominates; don't extrapolate a small-batch rate to large batches.

Related: [[Trust median and p90 over many runs, not the mean of a few, for latency on a noisy shared env]], [[Small per-run batches warm the JIT gradually across runs; large batches warm within one run]].

## Related

- [[Trust median and p90 over many runs]]
- [[not the mean of a few]]
- [[for latency on a noisy shared env]]
- [[Small per-run batches warm the JIT gradually across runs; large batches warm within one run]]
