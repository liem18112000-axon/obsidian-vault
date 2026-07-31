---
ai_hash: 44e5602d4601a55e
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
aliases:
- HyperLogLog
- HLL
created: 2026-06-16
entities: []
source: session 2026-06-16
status: seedling
tags:
- hyperloglog
- hll
- count
- cardinality
- luz-docs
title: HyperLogLog estimates distinct count in constant memory and is mergeable
type: concept
---

# HyperLogLog estimates distinct count in constant memory and is mergeable

**HyperLogLog (HLL)** estimates the number of *distinct* elements in **fixed, tiny memory** with a small bounded error.

Mechanism:
1. Hash each element to a uniform bit string.
2. First `p` bits pick a **register** (bucket); there are `m = 2^p` registers.
3. In the remaining bits, count leading zeros + 1 = rho. Keep the **max rho** per register.
4. A harmonic-mean-based estimator over all registers gives the cardinality. Intuition: a long run of leading zeros is rare, so a high max rho implies many distinct elements.

**Sizing:** `m = 2^14` registers ~ **12 KB** gives ~**0.8%** standard error. Memory is **constant** regardless of how many documents.

**Why it fits the union model:** HLLs are **mergeable** — union of two HLLs = register-wise **max**. So precompute one HLL per security code, merge the user's codes (+ public), then estimate. Same union shape as [[Visible-document count as cardinality of a bitmap union]] but with sketches.

**Limits:** approximate only (~1-2% error, both directions); **count-only** — cannot enumerate documents, cannot reliably subtract sets. For a security-facing number, exact ([[Roaring bitmaps give exact set cardinality via chunked containers]]) is usually safer; HLL is for extreme-scale, approximate-OK display counts.

## Related

- [[Visible-document count as cardinality of a bitmap union]]
- [[Roaring bitmaps give exact set cardinality via chunked containers]]
- [[1 Projects/luz-docs/count/optimize/Count-scaling path fan-out first, Roaring next, HyperLogLog for approximate]]

%% ai-graph-start %%

**Related notes:**
- [[HyperLogLog cardinality estimation mechanism (hash, register, streak-length)]]
- [[Visible-document count as cardinality of a bitmap union]]
- [[HyperLogLog error in the small-range (linear-counting) regime]]
- [[luz_docs countN badge can use HyperLogLog with a fuzzy-zone fallback]]
- [[Sketch merge (register-wise max) only answers union queries, never AND]]

%% ai-graph-end %%