---
title: "HyperLogLog error in the small-range (linear-counting) regime"
created: 2026-07-09
type: concept
status: seedling
source: "luz_docs count-estimate research, 2026-07-09"
tags: [hyperloglog, cardinality-estimation, probabilistic-data-structures, algorithms]
---

# HyperLogLog error in the small-range (linear-counting) regime

HLL's textbook error bound (relative error ≈ `1.04/√m`) is asymptotic — it holds only once true cardinality `n` is comparable to or larger than the register count `m`. When `n ≪ m`, HLL switches to the **linear-counting** estimator `E = -m·ln(V/m)` (V = registers still at zero), and the error there is **absolute**: `σ ≈ n/√(2m)`.

**Why it matters.** A "count > 999" badge with `m = 16384` (2^14, ~12 KB) sits deep in the small-range regime: σ ≈ 5.5, so the 95% CI is ≈ 999 ± 11 documents — a real, calculable boundary error, not rounding noise. Tightening it costs quadratically: hitting absolute error δ at cardinality n needs `m ≥ n²/(2δ²)`, so m approaches n itself and the fixed-size sketch loses its point.

**Fix for a boundary decision:** don't grow m. Put a **fuzzy zone** around the threshold (e.g. ±5%) and fall back to an exact check when the estimate lands inside it; trust the point estimate only outside the band. HLL is a legitimate fit for "about N" or clearly-over/under, never for "exactly at the boundary".

## Related

- [[HyperLogLog cardinality estimation mechanism (hash, register, streak-length)]]
- [[luz_docs count>N badge can use HyperLogLog with a fuzzy-zone fallback]]
- [[luz_docs estimated-count POC drops CAS and backfill gate]]
