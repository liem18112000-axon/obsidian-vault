---
title: "HyperLogLog error in the small-range (linear-counting) regime"
created: 2026-07-09
type: concept
status: seedling
source: "luz_docs count-estimate research, 2026-07-09"
tags: [hyperloglog, cardinality-estimation, probabilistic-data-structures, algorithms]
---

# HyperLogLog error in the small-range (linear-counting) regime

HyperLogLog's well-known error bound (relative error ~= 1.04/sqrt(m)) is an asymptotic figure that only holds when the true cardinality n is large relative to the register count m. When n << m, HLL switches to a **linear-counting** estimator instead: E = -m * ln(V/m), where V is the number of registers still at zero.

In that small-range regime the absolute error follows a different formula: sigma ~= n / sqrt(2m). This is *smaller* in absolute terms than the large-n formula would suggest, but it still sets a real floor: to get a target absolute error of delta at cardinality n, you need m >= n^2 / (2*delta^2). For a fixed small delta (say, 1-2 units), that m grows with the *square* of n, so tightening the error band toward exact eventually requires a register count approaching n itself.

Practical implication: HLL is well suited to answering "about n" or "over/under a threshold with some margin" for a fixed n, but the closer you need to pin down a specific boundary crossing (e.g. is the true count exactly above or below 999), the more register memory you burn chasing precision that never actually reaches exact. The right fix for a boundary decision isn't a bigger m — it's a fuzzy zone around the threshold that falls back to an exact check, rather than trusting the point estimate near the boundary.

Worked example: n=999, m=16384 (2^14, ~12KB) gives sigma ~= 5.5, so a 95% CI is roughly 999 +/- 11 documents.

## Related

- [[luz_docs count>N badge can use HyperLogLog with a fuzzy-zone fallback]]
