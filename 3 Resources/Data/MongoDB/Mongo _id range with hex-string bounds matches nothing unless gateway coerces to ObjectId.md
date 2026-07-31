---
title: "Mongo _id range with hex-string bounds matches nothing unless gateway coerces to ObjectId"
created: 2026-06-16
type: lesson
status: seedling
source: "LUZ-154613 session 2026-06-16"
tags: [mongodb, objectid, bson, gotcha, luz-docs]
---

# Mongo _id range with hex-string bounds matches nothing unless gateway coerces to ObjectId

MongoDB `_id` is a BSON ObjectId (type 7). A range query whose bounds are 24-char **hex strings** — `{_id:{$gte:"<hex>",$lt:"<hex>"}}` — does NOT compare numerically against ObjectIds. BSON compares across types by a fixed *canonical type order* where ObjectId sorts BEFORE String. So a String lower-bound `$gte` is greater than every ObjectId → matches 0 docs; a String `$lt` upper-bound → matches ALL docs. Net: string-bounded _id ranges silently return wrong results, not an error.

It only works if something coerces the string bounds into real ObjectId. In luz-docs the luz_jsonstore gateway coerces string _id → ObjectId for equality and $in lookups; the parallelize fan-out count (LUZ-154613) ASSUMES that coercion also fires inside $gte/$lt range operators (DESIGN §6: 'no $oid extended JSON needed'). That assumption is load-bearing and must be verified against the gateway — if it only coerces top-level equality/$in values, the ranged sub-counts mismatch.

Verify empirically: compare a K>1 fan-out count to a K=1 single count for the same tenant+filter; if they differ (one bucket ≈ full collection, others ≈ 0), bounds aren't coerced. Or capture the real Mongo pipeline (luz-skill-mongo-query-recorder) and check whether _id arrived as ObjectId(..) or a quoted string.

Fix if not coerced: send bounds as extended JSON {"$oid":"<hex>"}, add range-operator coercion in the gateway, or partition on a different key.

## Related

- [[Parallelize visible-doc count by fan-out over _id ranges (luz-docs)]]

## Empirical confirmation (LUZ-154613, dev tenant d0783310, 128k docs)
Proven on a live local luz-docs against dev luz_jsonstore:
- K=1 single count → **128000** (correct).
- K=4 fan-out → **0**. All 4 ranged sub-counts hit `luz_jsonstore .../documents/count`, each returned HTTP 200 with body `0`.

So the gateway accepts the `{$and:[{}, {_id:{$gte:"<hex>",$lt:"<hex>"}}]}` query but matches nothing — it does NOT coerce string `_id` bounds inside `$gte`/`$lt` (only equality/$in). My earlier theoretical "open-ended boundary ranges absorb all so the sum stays exact" reasoning was WRONG: empirically every range (incl. the open `$lt`-only and `$gte`-only ends) returned 0. Net = a **silent undercount to 0** (HTTP 200, no exception → fail-loud never trips). Worst failure mode for a security-visibility count.

Fix not yet applied: send bounds as extended JSON `{"$oid":"<hex>"}`, or add range-operator _id coercion in luz_jsonstore.
