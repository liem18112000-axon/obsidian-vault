---
title: "Roaring bitmaps for exact visible-document count removes the doc-times-code amplification"
created: 2026-06-16
type: concept
status: seedling
source: "LUZ-154613 session 2026-06-16"
tags: [luz-docs, roaring, bitmap, count, hyperloglog, performance]
---

# Roaring bitmaps for exact visible-document count removes the doc-times-code amplification

Exact visible-document count via Roaring bitmaps (luz-docs, alternative to the _shard count fan-out).

Problem: count of docs visible to a user = public OR (docs intersecting any of the user's security codes). The index walk pays for every (doc x matching-code) entry then dedups by RecordId, so work ∝ amplification not the answer — the heavy-user p99 tail. Fan-out parallelises that work; bitmaps REMOVE it.

Idea: model 'set of doc ids' as a bitmap (bit i = doc i present). Precompute ONE bitmap per security code (the docs carrying it) + a public bitmap. Answer = OR the user's code-bitmaps + public, then popcount. Dedup is free (a doc in 5 codes is one bit). Work ∝ number-of-codes, not doc×code entries; popcount is ~constant.

Why Roaring not a plain bitmap: a bitmap over the 2^96 ObjectId space is absurd. Roaring maps docs to dense ints [0,N), splits into 64K chunks, and stores each chunk as array (sparse) / bitmap (dense) / run-length (contiguous) — compressed in memory AND operated on without decompressing. OR/AND go chunk-by-chunk picking the fast path. A code over 1M docs ≈ tens–hundreds of KB, OR-ed in microseconds.

Exact vs HyperLogLog: Roaring is a real set → true distinct count. HLL is the sibling — ~12KB fixed, ~1-2% error, no per-doc bits — for when approximate + tiny fixed memory is acceptable.

Tradeoff (why DESIGN §8 says 'decide separately'): must MAINTAIN the per-code bitmaps on every create/delete/security change (write-path work + a stable doc-id↔int mapping), and keep them hot. Bigger change than fan-out, but attacks the root: heavy count goes from seconds to a sub-ms popcount regardless of code/doc counts.

vs _shard fan-out: fan-out spreads the SAME amplified work across K cores (ceiling = cores, needs CPU + hot index); Roaring eliminates the work (algorithmic). Complementary — Roaring/HLL is the next lever when fan-out's ceiling isn't enough.

## Related

- [[Frozen JsonStore gateway makes _id-range count fan-out a dead end — pivot to bitmap/HLL]]
