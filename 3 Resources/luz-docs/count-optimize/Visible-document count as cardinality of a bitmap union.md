---
title: "Visible-document count as cardinality of a bitmap union"
created: 2026-06-16
type: concept
status: seedling
source: "session 2026-06-16"
tags: [luz-docs, bitmap, count, materialize]
---

# Visible-document count as cardinality of a bitmap union

A user's visible-document count can be computed as the **cardinality of a union of precomputed bitmaps**, instead of scanning matching index entries.

Model each security code's document set as a bitmap (one bit per document; 1 = document carries that code). The public set is another bitmap. Then:

```
visible = | publicSet OR codeSet[c1] OR codeSet[c2] OR ... |   (for each c in user.codes)
```

- Union of bitmaps = bitwise **OR**; count = **popcount** of the result.
- **Key win:** a document in several of the user's codes collapses to a single 1-bit after OR. The per-`(doc × code)` amplification that makes the naive count slow for heavy users disappears — OR + popcount cost scales with bitmap size, not with the number of (doc × code) matches.

The two concrete representations are [[Roaring bitmaps give exact set cardinality via chunked containers]] (exact) and [[HyperLogLog estimates distinct count in constant memory and is mergeable]] (approximate). See [[Count-scaling path: fan-out first, Roaring next, HyperLogLog for approximate]] for when to use it.

## Related

- [[Roaring bitmaps give exact set cardinality via chunked containers]]
- [[HyperLogLog estimates distinct count in constant memory and is mergeable]]
- [[Count-scaling path: fan-out first]]
- [[Roaring next]]
- [[HyperLogLog for approximate]]
