---
title: "Sketch merge (register-wise max) only answers union queries, never AND"
created: 2026-07-09
type: lesson
status: seedling
source: "luz_docs docs/count-estimate research, 2026-07-09 — caught and fixed a wrong AND example in a diagram"
tags: [hyperloglog, cardinality-estimation, probabilistic-data-structures, gotcha]
---

# Sketch merge (register-wise max) only answers union queries, never AND

Two HyperLogLog (or any similar cardinality-sketch) registers combine by taking the element-wise max across their register arrays — and this operation computes the cardinality of the UNION of the two underlying sets, never the intersection.

Why: a register holds "the longest rare-streak seen by anything that landed here." If you pool two groups into one imaginary group, the longest streak in the pooled group is exactly the max of the two groups' longest streaks, register by register — that is what max() computes. But by taking the max you throw away *which* sketch each winning value came from, so there is no way to recover "how many items are common to both sets" (an intersection/AND) from the merged registers alone.

Concretely: a query like "folder = F123 OR tag = invoice" can be answered by merging the folder and tag sketches (union). A query like "folder = F123 AND tag = invoice" CANNOT be answered this way — it needs a different technique entirely (an exact Roaring-bitmap AND over per-dimension bitmaps, or a Jaccard-similarity-based cardinality estimator for approximate intersections).

I caught this as an actual mistake while writing luz_docs docs/count-estimate material: an earlier diagram used "folder = F123 AND tag = invoice" as a worked example of a query that a merged HLL sketch could answer — that was wrong and had to be corrected to an OR example. The gotcha is worth remembering generally: any time a design doc says "we can precompute per-dimension sketches and combine them at query time," always check whether the query"'s boolean combinator is OR (merge-safe) or AND (not merge-safe) before assuming the sketch approach applies.

See [[HyperLogLog cardinality estimation mechanism (hash, register, streak-length)]] and [[luz_docs count>N badge can use HyperLogLog with a fuzzy-zone fallback]].

## Related

- [[HyperLogLog cardinality estimation mechanism (hash]]
- [[register]]
- [[streak-length)]]
- [[luz_docs count>N badge can use HyperLogLog with a fuzzy-zone fallback]]
