---
title: "HyperLogLog small-range error needs linear-counting correction"
created: 2026-07-09
type: concept
status: seedling
source: "session 2026-07-09, luz_docs count-estimate research"
tags: [hyperloglog, cardinality-estimation, algorithms]
---

# HyperLogLog small-range error needs linear-counting correction

HyperLogLog error is NOT the textbook asymptotic 1.04/√m once true cardinality n is much smaller than the register count m — that formula only holds when n≈m or larger. In the small-range regime, HLL's own estimator switches to linear counting, E = -m·ln(V/m) where V is the fraction of empty registers, and the error there is σ ≈ n/√(2m) in absolute document count.

**Why it matters:** checking "count > 999" with m=16384 registers is deep in the small-range regime (n≪m), so the relevant error band is ~±11 docs at 95% CI — a real, calculable boundary error, not a rounding curiosity. Tightening that band toward exact requires m approaching n itself, which defeats the point of a fixed-size sketch. So HLL is a legitimate fit for an "about N" badge, but a raw point estimate can't be trusted right at a boundary — it needs a fuzzy zone (e.g. ±5% of the threshold) that falls back to an exact check when the estimate lands inside it.

Related: [[luz_docs estimated-count POC drops CAS and backfill gate]].

## Related

- [[luz_docs estimated-count POC drops CAS and backfill gate]]
