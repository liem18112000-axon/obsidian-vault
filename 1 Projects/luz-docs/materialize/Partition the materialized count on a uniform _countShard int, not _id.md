---
title: "Partition the materialized count on a uniform _countShard int, not _id"
created: 2026-06-16
type: howto
status: seedling
source: "LUZ-154613 session 2026-06-16"
tags: [luz-docs, materialize, mongodb, sharding, performance, design]
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

- [[Frozen JsonStore gateway makes _id-range count fan-out a dead end — pivot to bitmap/HLL]]
- [[MongoDB $expr + $toObjectId for _id range is correct but does not use the _id index (full scan)]]
