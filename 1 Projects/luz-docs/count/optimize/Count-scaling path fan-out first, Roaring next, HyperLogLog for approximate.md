---
ai_hash: d34b01e86eccbb29
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-16
entities:
- Count-scaling path
- Fan-out first
- Roaring next
- HyperLogLog for approximate
- eArchive visible-document count
- K concurrent sub-counts
- _id ranges
- partials
- CPU core count
- resident index
- Roaring bitmaps
- exact set cardinality
- chunked containers
- write-path
- storage
- bitmap maintenance
- ObjectId->ordinal dictionary
- persistence
- HyperLogLog
- distinct count
- constant memory
- mergeable
- extreme-scale tenants
- approximate count
- exactness
- enumeration
- security-facing number
- Visible-document count as cardinality of a bitmap union
- luz.docs.materialize.count-fanout-partitions
- amplified work
- original query
- luz_docs/docs/bitmap-count-investigation.md
- parallelize DESIGN.md
- excalidraw diagrams
- Roaring bitmaps give exact set cardinality via chunked containers
- HyperLogLog estimates distinct count in constant memory and is mergeable
source: session 2026-06-16
status: seedling
tags:
- luz-docs
- count
- materialize
- decision
title: 'Count-scaling path: fan-out first, Roaring next, HyperLogLog for approximate'
type: argument
---

# Count-scaling path: fan-out first, Roaring next, HyperLogLog for approximate

Decision order for scaling the eArchive visible-document count, cheapest/safest change first:

1. **Fan-out first (ship now).** Split one count into K concurrent sub-counts over disjoint `_id` ranges, sum the partials. Small, isolated, config-gated (`luz.docs.materialize.count-fanout-partitions`), exact regardless of K. Parallelises the amplified work but does **not** remove it — ceiling = CPU core count. Only helps when CPU-bound with spare cores and a resident index.

2. **Roaring next (only if a big-count tail persists).** [[Roaring bitmaps give exact set cardinality via chunked containers]] removes the amplification entirely and stays exact. Budget it as a **write-path + storage** change (bitmap maintenance + ObjectId->ordinal dictionary + persistence), not a read tweak.

3. **HyperLogLog in reserve.** [[HyperLogLog estimates distinct count in constant memory and is mergeable]] for extreme-scale tenants where the count is purely informational and approximate is acceptable. Constant memory, but gives up exactness and enumeration — risky for a security-facing number.

Rationale: each step is a bigger change than the last; only escalate on evidence the previous ceiling is actually being hit. All three share the [[Visible-document count as cardinality of a bitmap union]] union model except fan-out, which keeps the original query.

Source: `luz_docs/docs/bitmap-count-investigation.md` (+ 3 excalidraw diagrams) and the parallelize DESIGN.md.

## Related

- [[Visible-document count as cardinality of a bitmap union]]
- [[Roaring bitmaps give exact set cardinality via chunked containers]]
- [[HyperLogLog estimates distinct count in constant memory and is mergeable]]

%% ai-graph-start %%

**Related notes:**
- [[Visible-document count as cardinality of a bitmap union]]
- [[Levers to optimise the visible-document count beyond _shard fan-out]]
- [[Frozen JsonStore gateway makes _id-range count fan-out a dead end — pivot to bitmapHLL]]
- [[BitmapHLL counts supersede fan-out; they don't combine with it]]
- [[Divide-and-Conquer Visible-Document Count]]

**Relations:**
- Count-scaling path — *addresses* — eArchive visible-document count
- Count-scaling path — *includes step* — Fan-out first
- Count-scaling path — *includes step* — Roaring next
- Count-scaling path — *includes step* — HyperLogLog for approximate
- Fan-out first — *splits* — eArchive visible-document count
- eArchive visible-document count — *into* — K concurrent sub-counts
- K concurrent sub-counts — *over* — _id ranges
- Fan-out first — *sums* — partials
- Fan-out first — *is configured by* — luz.docs.materialize.count-fanout-partitions
- Fan-out first — *parallelises* — amplified work
- Fan-out first — *ceiling is* — CPU core count
- Fan-out first — *requires* — resident index
- Roaring next — *uses* — Roaring bitmaps
- Roaring bitmaps — *provide* — exact set cardinality
- Roaring bitmaps — *use* — chunked containers
- Roaring next — *removes* — amplified work
- Roaring next — *is a* — write-path
- Roaring next — *is a* — storage
- Roaring next — *involves* — bitmap maintenance
- Roaring next — *involves* — ObjectId->ordinal dictionary
- Roaring next — *involves* — persistence
- HyperLogLog for approximate — *uses* — HyperLogLog
- HyperLogLog — *estimates* — distinct count
- HyperLogLog — *uses* — constant memory
- HyperLogLog — *is* — mergeable
- HyperLogLog for approximate — *is for* — extreme-scale tenants
- HyperLogLog for approximate — *accepts* — approximate count
- HyperLogLog — *gives up* — exactness
- HyperLogLog — *gives up* — enumeration
- HyperLogLog for approximate — *is risky for* — security-facing number
- Roaring next — *shares model* — Visible-document count as cardinality of a bitmap union
- HyperLogLog for approximate — *shares model* — Visible-document count as cardinality of a bitmap union
- Fan-out first — *keeps* — original query
- Count-scaling path — *source* — luz_docs/docs/bitmap-count-investigation.md
- Count-scaling path — *source* — parallelize DESIGN.md
- luz_docs/docs/bitmap-count-investigation.md — *includes* — excalidraw diagrams
- Visible-document count as cardinality of a bitmap union — *is related to* — Count-scaling path
- Roaring bitmaps give exact set cardinality via chunked containers — *describes* — Roaring bitmaps
- HyperLogLog estimates distinct count in constant memory and is mergeable — *describes* — HyperLogLog

%% ai-graph-end %%