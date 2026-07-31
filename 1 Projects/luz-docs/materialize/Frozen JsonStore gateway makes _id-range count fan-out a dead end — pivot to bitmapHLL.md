---
ai_hash: 27bf01fa66c7d6e2
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-16
entities:
- JsonStore gateway
- _id-range count fan-out
- bitmap/HLL
- luz_jsonstore
- MongoDB
- _id RANGES
- ObjectId
- $in
- equality
- $gte
- $lt
- $expr
- $toObjectId
- K sub-counts
- Quantile boundaries
- Roaring bitmap
- HyperLogLog
- luz.docs.materialize.count-fanout-partitions
- amplification
- bitmap-count-investigation.md
- _id index
- Divide-and-Conquer Visible-Document Count
- performance
- client
- index-seeking _id range
- full-scan
- scan work
- gateway change
- amplified work
- p99 tail
- hex _id string
- union counts
- MongoDB $expr + $toObjectId
- amplification-removing approach
- luz_docs
source: LUZ-154613 session 2026-06-16
status: seedling
tags:
- luz-docs
- materialize
- mongodb
- performance
- decision
- objectid
title: Frozen JsonStore gateway makes _id-range count fan-out a dead end — pivot to
  bitmap/HLL
type: argument
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

%% ai-graph-start %%

**Related notes:**
- [[MongoDB $expr + $toObjectId for _id range is correct but does not use the _id index (full scan)]]
- [[Partition the materialized count on a uniform _countShard int, not _id]]
- [[Divide-and-Conquer Visible-Document Count]]
- [[Mongo _id range with hex-string bounds matches nothing unless gateway coerces to ObjectId]]
- [[Levers to optimise the visible-document count beyond _shard fan-out]]

**Relations:**
- JsonStore gateway — *causes* — _id-range count fan-out a dead end
- _id-range count fan-out — *pivot to* — bitmap/HLL
- luz_jsonstore — *is a gateway for* — MongoDB
- luz_jsonstore — *has constraint* — cannot be changed
- Divide-and-Conquer Visible-Document Count — *partitions on* — _id RANGES
- Divide-and-Conquer Visible-Document Count — *is a dead end for* — performance
- JsonStore gateway — *coerces* — hex _id string
- JsonStore gateway — *coerces to* — ObjectId
- ObjectId — *is used with* — $in
- ObjectId — *is used with* — equality
- ObjectId — *is not used with* — $gte
- ObjectId — *is not used with* — $lt
- index-seeking _id range — *cannot be expressed from* — client
- client-side _id range — *uses* — MongoDB $expr + $toObjectId
- MongoDB $expr + $toObjectId — *causes* — full-scan
- MongoDB $expr + $toObjectId — *does not use* — _id index
- K sub-counts — *cannot be* — correct AND fast
- Quantile boundaries — *balances* — scan work
- Quantile boundaries — *does not make* — indexed
- fan-out — *status* — OFF
- luz.docs.materialize.count-fanout-partitions — *set to* — 1
- amplification-removing approach — *includes* — Roaring bitmap
- amplification-removing approach — *includes* — HyperLogLog
- amplification-removing approach — *removes* — amplification
- Roaring bitmap — *is* — exact
- HyperLogLog — *is* — approximate
- Roaring bitmap — *provides* — union counts
- HyperLogLog — *provides* — union counts
- union counts — *are* — count-side
- union counts — *requires no* — gateway change
- Fan-out — *parallelises* — amplified work
- Fan-out — *never removes* — amplification
- bitmap-count-investigation.md — *is a document in* — luz_docs
- MongoDB $expr + $toObjectId — *is for* — _id range
- MongoDB $expr + $toObjectId — *is* — correct
- MongoDB $expr + $toObjectId — *does not use* — _id index
- MongoDB $expr + $toObjectId — *causes* — full-scan
- Divide-and-Conquer Visible-Document Count — *is a* — project
- amplification — *affects* — p99 tail

%% ai-graph-end %%