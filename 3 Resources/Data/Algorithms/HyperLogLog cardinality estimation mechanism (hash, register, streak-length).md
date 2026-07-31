---
ai_hash: 82c1135d8f39ef91
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-09
entities: []
source: luz_docs docs/count-estimate/HOW-HYPERLOGLOG-WORKS.md, 2026-07-09
status: seedling
tags:
- hyperloglog
- cardinality-estimation
- probabilistic-data-structures
- algorithms
title: HyperLogLog cardinality estimation mechanism (hash, register, streak-length)
type: concept
---

# HyperLogLog cardinality estimation mechanism (hash, register, streak-length)

HyperLogLog estimates how many distinct items were seen without storing or listing any of them, using a "rare long streak" trick and averaging it across many independent slots.

The intuition: hash each item into a bit string that looks like a sequence of fair coin flips. Read each bit as heads (0, keep going) or tails (1, stop) and count the leading zeros before the first 1 — call this rho. A streak of length k only happens with probability 1/2^(k+1) per item, so a big rho seen anywhere is weak evidence that *many* items were hashed (since rare events get more likely to appear at least once as the trial count grows).

One streak number alone is far too noisy to trust (it depends on a single random event). The fix: split the hash into two parts. A few bits pick one of m "registers" (buckets); the rest of the bits compute rho as above. Each register keeps only the MAXIMUM rho ever seen by an item that landed in it. This runs the same rare-streak experiment m times in parallel and independently, and averaging m independent noisy signals cancels most of the noise — error shrinks roughly as 1/sqrt(m).

To turn the m register values into one count estimate, combine them with a bias-corrected harmonic-mean-style formula: E = alpha_m * m^2 / sum(2^-register_i), where alpha_m is a small constant depending only on m. When the estimate would be small relative to m (below ~2.5m), switch to a more accurate "linear counting" formula instead: E = -m * ln(V/m), where V is the number of registers still at zero (an empty register is strong direct evidence of scarcity, more informative than the general-purpose harmonic mean in that regime).

See [[HyperLogLog error in the small-range (linear-counting) regime]] for the error-magnitude math this mechanism produces, and [[Sketch merge (register-wise max) only answers union queries, never AND]] for how two sketches combine.

## Related

- [[HyperLogLog error in the small-range (linear-counting) regime]]
- [[3 Resources/Data/Algorithms/Sketch merge (register-wise max) only answers union queries, never AND]]

%% ai-graph-start %%

**Related notes:**
- [[HyperLogLog estimates distinct count in constant memory and is mergeable]]
- [[HyperLogLog error in the small-range (linear-counting) regime]]
- [[Sketch merge (register-wise max) only answers union queries, never AND]]
- [[Visible-document count as cardinality of a bitmap union]]
- [[luz_docs documentscount is scan-bound and cannot reach sub-second at 128k]]

%% ai-graph-end %%