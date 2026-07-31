---
ai_hash: 57122e40170dd21d
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-17
entities: []
source: luz_docs LUZ-154613 2026-06-17
status: seedling
tags:
- performance
- mongodb
- bitmap
- roaring
- hyperloglog
- counting
title: Bitmap/HLL counts supersede fan-out; they don't combine with it
type: lesson
---

# Bitmap/HLL counts supersede fan-out; they don't combine with it

A slow security-facing visible-document count (`count where _isPublic OR _effectiveSecurityClassCodes intersects user.codes`) is slow because of **amplification**: a multikey `$in` produces one index entry per (doc x matching code), and counting DISTINCT docs needs a dedup scan. Work scales with the (doc x code) entries, not the answer.

Two ways to attack it, and they are NOT complementary:
- **Fan-out (K _shard buckets, run concurrently)** only PARALLELIZES the amplified scan across cores. Same total work, less wall-clock. Ceiling ~= number of executor threads (measured floor ~3s on a 6-thread pool, non-sharded replica set).
- **Bitmaps (Roaring, exact) / HyperLogLog (approx)** REMOVE the amplification: precompute one set per security code, union (OR / register-max) the users codes at query time, then popcount/estimate. That is sub-millisecond regardless of doc count.

Key consequence: bitmaps/HLL make the count sub-ms, which makes fan-out **redundant** for the read — they REPLACE it, they do not "join" K to boost it. Once the scan is gone there is nothing for K to parallelize. So pick a ceiling: keep fan-out (~3s, tiny isolated change) OR go Roaring (~ms, but a write-path + storage project: per-code bitmaps + ObjectId->ordinal dictionary + persistence).

Gotchas:
- A random `_shard` int (uniform in [0,2^30)) can NOT double as Roarings dense ordinal — it is sparse/collidable; you still need the ObjectId<->ordinal dictionary.
- You CANNOT precompute a single per-doc `_isVisible` flag, because visibility depends on the QUERYING users code set, not a fixed property. That per-user union is exactly what bitmaps solve and why index-coverage alone does not remove the amplification for the multi-code `$in`.
- HLL is mergeable and tiny but APPROXIMATE (+/-1-2%); for a security count an under-count is access-leak-shaped, so HLL is only safe for a cosmetic badge.

## Related

- [[Shard count fan-out most of the win is at K=4, diminishing returns after]]
- [[Widening fan-out threads doesn't help once MongoDB is the count bottleneck]]
- [[3 Resources/Data/MongoDB/Count fan-out _shard index must put _shard LAST in the compound key (ESR)]]
- [[MongoDB $facet buckets add no parallelism and defeat COUNT_SCAN]]

%% ai-graph-start %%

**Related notes:**
- [[Visible-document count as cardinality of a bitmap union]]
- [[Production security count is already COUNT_SCAN (covered); benchmark query's FETCH is inherent (multikey+$or+$nin)]]
- [[Count-scaling path fan-out first, Roaring next, HyperLogLog for approximate]]
- [[Levers to optimise the visible-document count beyond _shard fan-out]]
- [[Frozen JsonStore gateway makes _id-range count fan-out a dead end — pivot to bitmapHLL]]

%% ai-graph-end %%