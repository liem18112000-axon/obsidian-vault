---
title: "Frozen JsonStore gateway makes _id-range count fan-out a dead end — pivot to bitmap/HLL"
created: 2026-06-16
type: argument
status: seedling
source: "LUZ-154613 session 2026-06-16"
tags: [luz-docs, materialize, mongodb, performance, decision, objectid]
---

# Frozen JsonStore gateway makes _id-range count fan-out a dead end — pivot to bitmap/HLL

Under the hard constraint that luz_jsonstore (the MongoDB gateway) cannot be changed, the divide-and-conquer visible-document count that partitions on _id RANGES is a dead end for performance:

- The gateway coerces a hex _id string to ObjectId only for equality and $in (both index-using), NOT inside $gte/$lt. So a native, index-seeking _id range cannot be expressed from the client.
- The only correct client-side _id range is $expr + $toObjectId, which full-scans (no index) — proven: each of 4 sub-counts took ~9s including the empty ones; K=4 wall 13s vs single indexed count 3.5s. Slower.
- No other partition key works: no uniform numeric field exists on the docs, and adding a compound index is also a forbidden Mongo change.

Therefore K sub-counts can be correct OR fast, never both, with a frozen gateway. Quantile boundaries (DESIGN §8) only balance scan work; they don't make it indexed, so they don't help either.

Decision: keep fan-out OFF (luz.docs.materialize.count-fanout-partitions=1, the safe default) and pursue the amplification-removing approach instead — Roaring bitmap (exact) / HyperLogLog (approx) union counts, which are count-side and need no gateway change (see luz_docs/docs/bitmap-count-investigation.md). Fan-out parallelises amplified work but never removes the amplification (DESIGN §8), so it was the wrong lever for the heavy-user p99 tail.

## Related

- [[MongoDB $expr + $toObjectId for _id range is correct but does not use the _id index (full scan)]]
- [[1 Projects/luz-docs/count/optimize/Divide-and-Conquer Visible-Document Count]]
