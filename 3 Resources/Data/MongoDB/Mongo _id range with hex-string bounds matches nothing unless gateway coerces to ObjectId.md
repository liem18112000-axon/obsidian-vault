---
ai_hash: 24dfbff6a3a26ffe
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-16
entities: []
source: LUZ-154613 session 2026-06-16, empirically confirmed on dev tenant d0783310
status: seedling
tags:
- mongodb
- objectid
- bson
- gotcha
- luz-docs
title: Mongo _id range with hex-string bounds matches nothing unless gateway coerces
  to ObjectId
type: lesson
---

# Mongo _id range with hex-string bounds matches nothing unless gateway coerces to ObjectId

`_id` is a BSON ObjectId (type 7). A range query with 24-char **hex-string** bounds — `{_id:{$gte:"<hex>",$lt:"<hex>"}}` — does not compare numerically: BSON orders across types by a fixed canonical type order in which ObjectId sorts **before** String. So a String `$gte` lower bound exceeds every ObjectId (matches 0), and a String `$lt` upper bound matches all. Wrong results, no error.

It only works if something coerces the bounds to real ObjectId. The luz_jsonstore gateway coerces string `_id` → ObjectId for **equality and `$in` only** — not inside `$gte`/`$lt`. The parallelize fan-out count (LUZ-154613) assumed it did (DESIGN §6: "no $oid extended JSON needed"); that assumption was load-bearing and false.

**Empirical confirmation** (live local luz-docs → dev luz_jsonstore, 128k docs): K=1 single count → **128000**; K=4 fan-out → **0**, with all four sub-counts returning HTTP 200 body `0`. Even the open-ended `$gte`-only and `$lt`-only buckets returned 0, so the "boundary ranges absorb everything, the sum stays exact" reasoning is wrong. Net effect is a **silent undercount to 0** — HTTP 200, no exception, fail-loud never trips. Worst possible failure mode for a security-visibility count.

**Verify:** compare a K>1 fan-out count against K=1 for the same tenant+filter, or capture the real pipeline (`luz-skill-mongo-query-recorder`) and check whether `_id` arrived as `ObjectId(..)` or a quoted string.

**Fix:** send bounds as extended JSON `{"$oid":"<hex>"}`, add range-operator coercion in the gateway, or partition on a different key (a random `_shard` int avoids both this and the skew problem).

## Related

- [[MongoDB $expr + $toObjectId for _id range is correct but does not use the _id index (full scan)]]
- [[Count fan-out _shard index must put _shard LAST in the compound key (ESR)]]
- [[1 Projects/luz-docs/count/optimize/Divide-and-Conquer Visible-Document Count]]

%% ai-graph-start %%

**Related notes:**
- [[MongoDB $expr + $toObjectId for _id range is correct but does not use the _id index (full scan)]]
- [[Partition the materialized count on a uniform _countShard int, not _id]]
- [[Frozen JsonStore gateway makes _id-range count fan-out a dead end — pivot to bitmapHLL]]
- [[Divide-and-Conquer Visible-Document Count]]
- [[jsonstore $in vs $nin ObjectId conversion gap]]

%% ai-graph-end %%