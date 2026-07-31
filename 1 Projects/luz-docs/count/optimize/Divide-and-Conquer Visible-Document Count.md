---
title: "Divide-and-Conquer Visible-Document Count"
aliases:
  - "Parallelize visible-doc count by fan-out over _id ranges (luz-docs)"
created: 2026-06-16
type: howto
status: seedling
source: "LUZ-154613 (design + implementation), sessions 2026-05-30 / 2026-06-16"
tags: [luz-docs, materialize, mongodb, performance, count, fanout, objectid]
entities:
  - Divide-and-Conquer
  - Visible-Document Count
  - _id ranges
  - _isPublic
  - _effectiveSecurityClassCodes
  - luz.docs.materialize.count-fanout-partitions
  - MaterializeCountFanout
  - MaterializeRepository.countTotalDocumentByQuery
  - ch.klara.luz.docs.materialize.parallelize
  - ManagedExecutorService
  - LUZ-154613
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
