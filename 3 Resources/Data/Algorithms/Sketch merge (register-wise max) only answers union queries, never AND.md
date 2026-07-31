---
ai_hash: 1d6a669899e8ebb4
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-09
entities: []
source: luz_docs docs/count-estimate research, 2026-07-09 — caught a wrong AND example
  in a diagram
status: seedling
tags:
- hyperloglog
- cardinality-estimation
- probabilistic-data-structures
- gotcha
title: Sketch merge (register-wise max) only answers union queries, never AND
type: lesson
---

# Sketch merge (register-wise max) only answers union queries, never AND

Two HLL (or similar cardinality) sketches combine by element-wise `max()` over their register arrays. That yields the cardinality of the **UNION**, never the intersection.

**Why:** each register holds the longest rare-streak seen by anything that landed in it, so the pooled group's longest streak *is* the max of the two — that's exactly what merging computes. But the max discards *which* sketch contributed each winner, so nothing in the merged registers can tell you how many items were in both sets.

**Consequence for query planning:** `folder = F123 OR tag = invoice` is answerable by merging the folder and tag sketches. `folder = F123 AND tag = invoice` is **not** — an AND needs a different structure entirely: an exact Roaring-bitmap AND over per-dimension bitmaps, or a Jaccard-similarity-based estimator for an approximate intersection.

**Rule:** whenever a design says "precompute per-dimension sketches and combine at query time", check the boolean combinator first — OR is merge-safe, AND is not. (This exact error shipped in a luz_docs count-estimate diagram before being corrected.)

## Related

- [[HyperLogLog cardinality estimation mechanism (hash, register, streak-length)]]
- [[1 Projects/luz-docs/luz_docs countN badge can use HyperLogLog with a fuzzy-zone fallback]]

%% ai-graph-start %%

**Related notes:**
- [[HyperLogLog estimates distinct count in constant memory and is mergeable]]
- [[luz_docs countN badge can use HyperLogLog with a fuzzy-zone fallback]]
- [[HyperLogLog error in the small-range (linear-counting) regime]]
- [[HyperLogLog cardinality estimation mechanism (hash, register, streak-length)]]
- [[Visible-document count as cardinality of a bitmap union]]

%% ai-graph-end %%