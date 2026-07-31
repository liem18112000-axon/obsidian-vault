---
ai_hash: c29ef904d61265c6
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-16
entities: []
source: sessions 2026-06-16 (LUZ-154613)
status: seedling
tags:
- luz-docs
- bitmap
- roaring
- count
- materialize
- performance
title: Visible-document count as cardinality of a bitmap union
type: concept
---

# Visible-document count as cardinality of a bitmap union

A user's visible-document count = **cardinality of a union of precomputed bitmaps**, instead of scanning matching index entries.

Model each security code's document set as a bitmap (bit i = doc i carries that code); the public set is another bitmap:

```
visible = | publicSet OR codeSet[c1] OR codeSet[c2] OR ... |   (for each c in user.codes)
```

Union = bitwise **OR**; count = **popcount**.

**Why it matters — it removes the doc×code amplification.** The naive count (public OR docs intersecting any of the user's codes) makes the index walk pay for every `(doc × matching-code)` entry and then dedup by RecordId, so work scales with amplification, not with the answer — this is the heavy-user p99 tail. After OR, a doc carrying 5 of the user's codes is one bit: dedup is free and work scales with the **number of codes**, not doc×code entries.

**Representations:** [[Roaring bitmaps give exact set cardinality via chunked containers]] (exact — required for a security-facing number) and [[HyperLogLog estimates distinct count in constant memory and is mergeable]] (approximate, ~12 KB fixed, ~1–2% error). See [[1 Projects/luz-docs/count/optimize/Count-scaling path fan-out first, Roaring next, HyperLogLog for approximate]].

**Cost / why it is deferred (DESIGN §8 "decide separately"):** the per-code bitmaps plus a stable doc-id↔int ordinal dictionary must be **maintained on the write path** (create / delete / security-class change) and kept hot. That is a bigger change than `_shard` fan-out.

**vs `_shard` fan-out:** fan-out spreads the *same* amplified work across K cores (ceiling = cores, needs CPU + hot index); bitmaps **eliminate** the work algorithmically — heavy count goes from seconds to a sub-ms popcount regardless of code/doc counts. Complementary: bitmaps are the next lever once fan-out's ceiling is not enough.

## Related

- [[Roaring bitmaps give exact set cardinality via chunked containers]]
- [[HyperLogLog estimates distinct count in constant memory and is mergeable]]
- [[luz-docs parallelized count undercounts documents missing _shard]]
- [[1 Projects/luz-docs/materialize/Frozen JsonStore gateway makes _id-range count fan-out a dead end — pivot to bitmapHLL]]

%% ai-graph-start %%

**Related notes:**
- [[BitmapHLL counts supersede fan-out; they don't combine with it]]
- [[Levers to optimise the visible-document count beyond _shard fan-out]]
- [[Roaring bitmaps give exact set cardinality via chunked containers]]
- [[Divide-and-Conquer Visible-Document Count]]
- [[Count-scaling path fan-out first, Roaring next, HyperLogLog for approximate]]

%% ai-graph-end %%