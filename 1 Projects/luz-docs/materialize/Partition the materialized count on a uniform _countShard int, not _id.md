---
ai_hash: 2da20d71d2062ba2
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-16
entities:
- Partitioning
- materialized count
- _countShard
- _id
- luz_jsonstore
- range operators
- PLAIN types
- ObjectId
- indexes
- gateway
- divide-and-conquer count
- uniform integer shard field
- SHARD_SPACE
- _isPublic
- _effectiveSecurityClassCodes
- sub-count clause
- $expr
- $toObjectId
- hash field
- _idStr
- load-skew problem
- quantile boundary sampling
- backfill
- Mongo schema
- LUZ-154613
- materialize compute
- migration executor
- MongoDB
- JsonStore
- bitmapHLL
- _id index
- full scan
- _id-range count fan-out
- int
- string
source: LUZ-154613 session 2026-06-16
status: seedling
tags:
- luz-docs
- materialize
- mongodb
- sharding
- performance
- design
title: Partition the materialized count on a uniform _countShard int, not _id
type: howto
---

# Partition the materialized count on a uniform _countShard int, not _id

When the MongoDB gateway (luz_jsonstore) forwards range operators only for PLAIN types (int/string) but not ObjectId, and you may add indexes but not change the gateway, the right partition key for a divide-and-conquer count is a stored uniform integer shard field — NOT _id.

Design (luz-docs LUZ-154613):
- Add `_countShard` (int) = deterministic uniform hash of _id mapped into [0, SHARD_SPACE), e.g. SHARD_SPACE = 1<<30. Stamp it in the materialize compute alongside _isPublic/_effectiveSecurityClassCodes; backfill existing docs in the migration executor.
- Index {_effectiveSecurityClassCodes:1,_countShard:1} and {_isPublic:1,_countShard:1} so each sub-count seeks its int slice INSIDE the security-matched interval (the DESIGN §5 index-seek goal).
- Sub-count clause is plain: {_countShard:{$gte:lo,$lt:hi}} → gateway forwards natively → index used. No $oid, no $expr+$toObjectId (which scans), no Date coercion.

Why a hash field beats _id or _idStr: plain int dodges the ObjectId range-coercion wall; uniform hash spreads evenly so buckets balance with simple equal int cuts → the §8 load-skew problem disappears and quantile boundary sampling is unnecessary. Deterministic-from-_id makes the backfill idempotent.

This is correct + fast + balanced with no JsonStore change — the only constraint relaxation needed is permission to add the field + indexes (Mongo schema), which the team granted.

## Related

- [[1 Projects/luz-docs/materialize/Frozen JsonStore gateway makes _id-range count fan-out a dead end — pivot to bitmapHLL]]
- [[MongoDB $expr + $toObjectId for _id range is correct but does not use the _id index (full scan)]]

%% ai-graph-start %%

**Related notes:**
- [[Frozen JsonStore gateway makes _id-range count fan-out a dead end — pivot to bitmapHLL]]
- [[No existing luz-docs field works as a fan-out count partition key — survey]]
- [[MongoDB $expr + $toObjectId for _id range is correct but does not use the _id index (full scan)]]
- [[Divide-and-Conquer Visible-Document Count]]
- [[Mongo _id range with hex-string bounds matches nothing unless gateway coerces to ObjectId]]

**Relations:**
- materialized count — *uses* — Partitioning
- Partitioning — *on* — _countShard
- Partitioning — *not on* — _id
- luz_jsonstore — *forwards* — range operators
- range operators — *for* — PLAIN types
- luz_jsonstore — *does not forward* — range operators
- range operators — *does not forward for* — ObjectId
- PLAIN types — *includes* — int
- PLAIN types — *includes* — string
- _countShard — *is a* — uniform integer shard field
- _countShard — *is an* — int
- _countShard — *is a* — deterministic uniform hash of _id
- _countShard — *mapped into* — [0, SHARD_SPACE)
- LUZ-154613 — *is a* — Design
- Design — *adds* — _countShard
- _countShard — *stamped in* — materialize compute
- _isPublic — *stamped in* — materialize compute
- _effectiveSecurityClassCodes — *stamped in* — materialize compute
- _countShard — *backfilled in* — migration executor
- indexes — *include* — {_effectiveSecurityClassCodes:1,_countShard:1}
- indexes — *include* — {_isPublic:1,_countShard:1}
- sub-count clause — *is* — {_countShard:{$gte:lo,$lt:hi}}
- gateway — *forwards* — sub-count clause
- sub-count clause — *forwarded* — natively
- hash field — *beats* — _id
- hash field — *beats* — _idStr
- uniform hash — *spreads* — evenly
- uniform hash — *solves* — load-skew problem
- uniform hash — *makes unnecessary* — quantile boundary sampling
- backfill — *is* — idempotent
- Mongo schema — *allows adding* — field
- Mongo schema — *allows adding* — indexes
- MongoDB $expr + $toObjectId — *does not use* — _id index
- MongoDB $expr + $toObjectId — *causes* — full scan
- Frozen JsonStore gateway — *makes* — _id-range count fan-out
- _id-range count fan-out — *is a* — dead end
- _id-range count fan-out — *pivots to* — bitmapHLL
- _id — *is a* — ObjectId
- luz_jsonstore — *is a* — MongoDB gateway
- JsonStore — *is a* — gateway
- _id-range count fan-out — *requires* — pivot to bitmapHLL
- _countShard — *is a* — partition key
- materialized count — *uses* — _countShard as partition key
- _id — *is not a* — suitable partition key
- _countShard — *dodges* — ObjectId range-coercion wall
- _id — *causes* — ObjectId range-coercion wall
- _idStr — *causes* — ObjectId range-coercion wall
- luz_jsonstore — *is a* — JsonStore
- MongoDB — *uses* — gateway
- MongoDB — *uses* — indexes
- MongoDB — *uses* — _id
- MongoDB — *uses* — _countShard
- MongoDB — *uses* — _isPublic
- MongoDB — *uses* — _effectiveSecurityClassCodes
- MongoDB — *uses* — ObjectId
- MongoDB — *uses* — int
- MongoDB — *uses* — string
- MongoDB — *uses* — $expr
- MongoDB — *uses* — $toObjectId
- MongoDB — *uses* — _id index
- MongoDB — *uses* — full scan
- MongoDB — *uses* — bitmapHLL

%% ai-graph-end %%