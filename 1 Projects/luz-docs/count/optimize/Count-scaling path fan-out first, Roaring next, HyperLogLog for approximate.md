---
title: "Count-scaling path: fan-out first, Roaring next, HyperLogLog for approximate"
created: 2026-06-16
type: argument
status: seedling
source: "session 2026-06-16"
tags: [luz-docs, count, materialize, decision]
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
