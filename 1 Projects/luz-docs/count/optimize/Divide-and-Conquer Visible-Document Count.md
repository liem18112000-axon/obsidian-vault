---
ai_hash: 745a75b38e9c3a76
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
aliases:
- Parallelize visible-doc count by fan-out over _id ranges (luz-docs)
created: 2026-06-16
entities:
- Divide-and-Conquer Visible-Document Count
- K concurrent sub-counts
- _id ranges
- partials
- materialised visible-document total
- count(_isPublic == true OR _effectiveSecurityClassCodes ∩ user.codes)
- multikey index
- amplification factor
- record id
- engine
- thread
- core
- p99 tail
- _id
- 24-hex ObjectId
- number in [0, 2^96)
- K-1 evenly spaced cuts
- bound_i
- SPACE
- BigInteger.ONE.shiftLeft(96)
- 24-char zero-padded lowercase hex
- Sub-query i
- base filter
- ManagedExecutorService
- partial sum
- security-visibility total
- access-leak-shaped bug
- skew
- quantile boundaries
- real data
- LUZ-154613
- ch.klara.luz.docs.materialize.parallelize
- Mongo
- JsonStore gateway
- fan-out issues
- ordinary match queries
- jsonStore.countByFilter
- Bounds
- 24-char hex strings
- $oid
- extended-JSON wrapper
- MaterializeRepository.countTotalDocumentByQuery
- MaterializeCountFanout.count
- query
- ToIntFunction<JsonObject>
- MaterializeFacade
- luz.docs.materialize.count-fanout-partitions
- '@ConfigProperty(defaultValue="1") int'
- Index shape
- '{_effectiveSecurityClassCodes:1, _id:1}'
- '{_isPublic:1, _id:1}'
- SECOND key
- index seek
- security-matched interval
- post-filter
- Load skew
- ObjectIds
- creation timestamp
- uniform cuts
- uneven buckets
- CPU-bound
- spare cores
- resident index
- I/O
- cold index pages
- contention
- bitmap union
- Roaring
- HyperLogLog
- read-preference knob
- count path
- analytics secondary
- Divide-and-Conquer Count - Technical Points for Beginners
- Count-scaling path fan-out first, Roaring next, HyperLogLog for approximate
- Frozen JsonStore gateway makes _id-range count fan-out a dead end — pivot to bitmapHLL
- Partition the materialized count on a uniform _countShard int, not _id
- Overall diagram
- Raw picture diagram
- step frames s1-problem … s8-skew
source: LUZ-154613 (design + implementation), sessions 2026-05-30 / 2026-06-16
status: seedling
tags:
- luz-docs
- materialize
- mongodb
- performance
- count
- fanout
- objectid
title: Divide-and-Conquer Visible-Document Count
type: howto
---

# Divide-and-Conquer Visible-Document Count

> Split one slow count into **K concurrent sub-counts over disjoint `_id` ranges**, then sum the partials. Exact for any id distribution, because the ranges tile the whole space with no gap and no overlap.

Diagrams: [[divide-conquer-count.excalidraw|Overall]] · [[divide-conquer-count.png|Raw picture]] (step frames `s1-problem` … `s8-skew`).

## Why

The materialised visible-document total is `count(_isPublic == true OR _effectiveSecurityClassCodes ∩ user.codes)`. On a multikey index the engine pays for every `(document × matching-code)` index entry and then de-duplicates by record id, so work scales with that **amplification factor**, not with the answer. One request = one thread = one core → p99 tail for heavy users (many codes, many docs per code).

## Mechanism

- `_id` is a 24-hex ObjectId ≈ a number in `[0, 2^96)`. Place `K-1` evenly spaced cuts: `bound_i = SPACE * i / K` where `SPACE = BigInteger.ONE.shiftLeft(96)`, formatted as 24-char zero-padded lowercase hex.
- Sub-query `i` = base filter AND `{_id: {$gte: B(i-1), $lt: Bi}}`. First range open below (no `$gte`), last open above (no `$lt`).
- Run the K queries concurrently on a `ManagedExecutorService` (one per core), sum the partials.
- **Fail loud:** if any sub-count throws, the whole count fails — never return a partial sum. An under-count on a security-visibility total is an access-leak-shaped bug.
- If skew becomes a problem, swap the evenly-spaced cuts for quantile boundaries sampled from real data — that is the single point of change (correctness is unaffected either way).

## Implementation (LUZ-154613, package `ch.klara.luz.docs.materialize.parallelize`)

- **No Mongo change allowed** (Mongo sits behind the JsonStore gateway), so fan-out issues only ordinary match queries `{$and:[<base>, {_id:{$gte:'B(i-1)', $lt:'Bi'}}]}` through the existing `jsonStore.countByFilter`. Bounds are plain 24-char hex strings — the gateway already coerces `_id` string→ObjectId for eq/`$in`, so no `$oid` / extended-JSON wrapper is needed.
- **Wiring is one line:** `MaterializeRepository.countTotalDocumentByQuery` delegates to `MaterializeCountFanout.count(query, q -> jsonStore.countByFilter(...))`; the single count stays the per-range primitive, passed as a `ToIntFunction<JsonObject>`. `MaterializeFacade` above it is unchanged.
- **Config:** `luz.docs.materialize.count-fanout-partitions`, injected `@ConfigProperty(defaultValue="1") int`. `≤1` = fan-out OFF (safe default); `K` = K concurrent sub-counts.

## Limits

- **Index shape is mandatory:** `{_effectiveSecurityClassCodes:1, _id:1}` and `{_isPublic:1, _id:1}` — `_id` as the SECOND key so each range is an index seek *inside* the security-matched interval. Without it the range clause degrades to a post-filter: K× the work for zero gain.
- **Load skew:** ObjectIds carry a creation timestamp, so ids cluster by time and uniform cuts give uneven buckets. Correctness is unaffected; only the speedup caps.
- **Only helps when CPU-bound** with spare cores and a resident index. If the bottleneck is I/O (cold index pages), K sub-counts buy contention, not speed.
- **Amplification remains** — fan-out parallelises it, never removes it; ceiling = core count. Removing it needs a bitmap union (Roaring exact / HyperLogLog approximate).
- No read-preference knob exists on the count path, so heavy sub-counts cannot be routed to an analytics secondary.

## Related

- [[Divide-and-Conquer Count - Technical Points for Beginners]]
- [[1 Projects/luz-docs/count/optimize/Count-scaling path fan-out first, Roaring next, HyperLogLog for approximate]]
- [[1 Projects/luz-docs/materialize/Frozen JsonStore gateway makes _id-range count fan-out a dead end — pivot to bitmapHLL]]
- [[Partition the materialized count on a uniform _countShard int, not _id]]

%% ai-graph-start %%

**Related notes:**
- [[Partition the materialized count on a uniform _countShard int, not _id]]
- [[Levers to optimise the visible-document count beyond _shard fan-out]]
- [[Production security count is already COUNT_SCAN (covered); benchmark query's FETCH is inherent (multikey+$or+$nin)]]
- [[Frozen JsonStore gateway makes _id-range count fan-out a dead end — pivot to bitmapHLL]]
- [[Divide-and-Conquer Count - Technical Points for Beginners]]

**Relations:**
- Divide-and-Conquer Visible-Document Count — *splits* — slow count
- Divide-and-Conquer Visible-Document Count — *uses* — K concurrent sub-counts
- Divide-and-Conquer Visible-Document Count — *sums* — partials
- Divide-and-Conquer Visible-Document Count — *operates over* — _id ranges
- Divide-and-Conquer Visible-Document Count — *is for* — materialised visible-document total
- Divide-and-Conquer Visible-Document Count — *is implemented in* — LUZ-154613
- Divide-and-Conquer Visible-Document Count — *is implemented in package* — ch.klara.luz.docs.materialize.parallelize
- Divide-and-Conquer Visible-Document Count — *has diagram* — Overall diagram
- Divide-and-Conquer Visible-Document Count — *has diagram* — Raw picture diagram
- Divide-and-Conquer Visible-Document Count — *has related* — Divide-and-Conquer Count - Technical Points for Beginners
- Divide-and-Conquer Visible-Document Count — *has related* — Count-scaling path fan-out first, Roaring next, HyperLogLog for approximate
- Divide-and-Conquer Visible-Document Count — *has related* — Frozen JsonStore gateway makes _id-range count fan-out a dead end — pivot to bitmapHLL
- Divide-and-Conquer Visible-Document Count — *has related* — Partition the materialized count on a uniform _countShard int, not _id
- Divide-and-Conquer Visible-Document Count — *is configured by* — luz.docs.materialize.count-fanout-partitions
- Divide-and-Conquer Visible-Document Count — *requires* — Index shape
- Divide-and-Conquer Visible-Document Count — *is limited by* — Load skew
- Divide-and-Conquer Visible-Document Count — *is limited by* — amplification factor
- Divide-and-Conquer Visible-Document Count — *is limited by* — read-preference knob
- Divide-and-Conquer Visible-Document Count — *helps when* — CPU-bound
- Divide-and-Conquer Visible-Document Count — *helps when* — spare cores
- Divide-and-Conquer Visible-Document Count — *helps when* — resident index
- Divide-and-Conquer Visible-Document Count — *does not help when* — I/O
- Divide-and-Conquer Visible-Document Count — *does not help when* — cold index pages
- K concurrent sub-counts — *are over* — _id ranges
- K concurrent sub-counts — *are run concurrently on* — ManagedExecutorService
- K concurrent sub-counts — *produce* — partials
- _id ranges — *are based on* — _id
- _id ranges — *are defined by* — K-1 evenly spaced cuts
- _id ranges — *are defined by* — bound_i
- _id ranges — *are defined by* — SPACE
- _id ranges — *are formatted as* — 24-char zero-padded lowercase hex
- materialised visible-document total — *is* — count(_isPublic == true OR _effectiveSecurityClassCodes ∩ user.codes)
- count(_isPublic == true OR _effectiveSecurityClassCodes ∩ user.codes) — *uses* — multikey index
- count(_isPublic == true OR _effectiveSecurityClassCodes ∩ user.codes) — *causes* — amplification factor
- multikey index — *causes* — amplification factor
- amplification factor — *is caused by* — multikey index
- amplification factor — *is related to* — record id
- amplification factor — *is parallelised by* — Divide-and-Conquer Visible-Document Count
- amplification factor — *can be removed by* — bitmap union
- engine — *pays for* — index entry
- engine — *de-duplicates by* — record id
- thread — *is associated with* — One request
- core — *is associated with* — One thread
- _id — *is a* — 24-hex ObjectId
- _id — *is a* — number in [0, 2^96)
- _id — *carries* — creation timestamp
- ManagedExecutorService — *runs* — K concurrent sub-counts
- skew — *can be addressed by* — quantile boundaries
- skew — *is caused by* — ObjectIds
- skew — *is caused by* — creation timestamp
- skew — *is caused by* — uniform cuts
- skew — *results in* — uneven buckets
- LUZ-154613 — *implements* — Divide-and-Conquer Visible-Document Count
- ch.klara.luz.docs.materialize.parallelize — *is package for* — LUZ-154613
- Mongo — *sits behind* — JsonStore gateway
- Mongo — *does not allow* — Mongo change
- JsonStore gateway — *is behind* — Mongo
- JsonStore gateway — *coerces* — _id string
- JsonStore gateway — *coerces to* — ObjectId
- JsonStore gateway — *handles* — eq/$in
- JsonStore gateway — *routes* — ordinary match queries
- JsonStore gateway — *uses* — jsonStore.countByFilter
- ordinary match queries — *are routed through* — JsonStore gateway
- ordinary match queries — *use* — jsonStore.countByFilter
- jsonStore.countByFilter — *is used by* — ordinary match queries
- jsonStore.countByFilter — *is a* — single count
- jsonStore.countByFilter — *is a* — per-range primitive
- MaterializeRepository.countTotalDocumentByQuery — *delegates to* — MaterializeCountFanout.count
- MaterializeCountFanout.count — *is called by* — MaterializeRepository.countTotalDocumentByQuery
- MaterializeCountFanout.count — *takes* — query
- MaterializeCountFanout.count — *takes* — ToIntFunction<JsonObject>
- MaterializeFacade — *is above* — MaterializeRepository.countTotalDocumentByQuery
- luz.docs.materialize.count-fanout-partitions — *is a* — Config property
- luz.docs.materialize.count-fanout-partitions — *has default value* — 1
- luz.docs.materialize.count-fanout-partitions — *controls* — K concurrent sub-counts
- Index shape — *is mandatory for* — Divide-and-Conquer Visible-Document Count
- Index shape — *includes* — {_effectiveSecurityClassCodes:1, _id:1}
- Index shape — *includes* — {_isPublic:1, _id:1}
- Index shape — *requires* — SECOND key
- SECOND key — *enables* — index seek
- index seek — *is inside* — security-matched interval
- CPU-bound — *is a condition for* — Divide-and-Conquer Visible-Document Count
- spare cores — *is a condition for* — Divide-and-Conquer Visible-Document Count
- resident index — *is a condition for* — Divide-and-Conquer Visible-Document Count
- I/O — *is a bottleneck when* — cold index pages
- cold index pages — *cause* — I/O bottleneck
- bitmap union — *removes* — amplification factor
- bitmap union — *can be* — Roaring
- bitmap union — *can be* — HyperLogLog
- Roaring — *is a type of* — bitmap union
- Roaring — *is* — exact
- HyperLogLog — *is a type of* — bitmap union
- HyperLogLog — *is* — approximate
- read-preference knob — *does not exist on* — count path
- analytics secondary — *cannot be routed to by* — heavy sub-counts
- Overall diagram — *is a* — diagram
- Overall diagram — *illustrates* — Divide-and-Conquer Visible-Document Count
- Raw picture diagram — *is a* — diagram
- Raw picture diagram — *illustrates* — Divide-and-Conquer Visible-Document Count
- Raw picture diagram — *has details* — step frames s1-problem … s8-skew

%% ai-graph-end %%