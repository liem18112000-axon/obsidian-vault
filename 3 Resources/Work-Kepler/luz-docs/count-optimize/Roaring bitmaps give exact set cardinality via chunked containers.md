---
ai_hash: b0b275b51f378649
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
aliases:
- Roaring bitmap
created: 2026-06-16
entities: []
source: session 2026-06-16
status: seedling
tags:
- roaring
- bitmap
- count
- luz-docs
title: Roaring bitmaps give exact set cardinality via chunked containers
type: concept
---

# Roaring bitmaps give exact set cardinality via chunked containers

A **Roaring bitmap** stores a set of integer ids compactly while keeping exact membership and fast set ops.

How it compresses: split a 32-bit id into the **high 16 bits** (chunk key) and **low 16 bits** (position inside the chunk). Each chunk stores its members in one of three container types, chosen by density:

- **Array** container — sparse chunk (<= 4096 values): sorted list of 16-bit positions.
- **Bitmap** container — dense chunk: a 2^16-bit (8 KB) bitmap.
- **Run** container — long consecutive stretches: (start, length) run pairs.

Union/intersection run container-by-container; **cardinality is a cached popcount per container**, so counting is near-instant.

**Gotcha for luz:** a document `_id` is a 96-bit ObjectId — too wide and too sparse for a 32-bit bitmap. You must maintain an **ObjectId <-> int ordinal dictionary** and store the small ordinals. The real cost of adopting Roaring is therefore the **write path** (keeping per-code bitmaps + the ordinal dictionary in sync on create/delete/reclassify, plus persistence), not the read path.

Use this to back [[Visible-document count as cardinality of a bitmap union]] when an **exact** count is required.

## Related

- [[Visible-document count as cardinality of a bitmap union]]
- [[1 Projects/luz-docs/count/optimize/Count-scaling path fan-out first, Roaring next, HyperLogLog for approximate]]

%% ai-graph-start %%

**Related notes:**
- [[Visible-document count as cardinality of a bitmap union]]
- [[Count-scaling path fan-out first, Roaring next, HyperLogLog for approximate]]
- [[Frozen JsonStore gateway makes _id-range count fan-out a dead end — pivot to bitmapHLL]]
- [[BitmapHLL counts supersede fan-out; they don't combine with it]]
- [[HyperLogLog estimates distinct count in constant memory and is mergeable]]

%% ai-graph-end %%