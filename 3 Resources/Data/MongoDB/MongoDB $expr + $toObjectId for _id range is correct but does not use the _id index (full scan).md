---
title: "MongoDB $expr + $toObjectId for _id range is correct but does not use the _id index (full scan)"
created: 2026-06-16
type: lesson
status: seedling
source: "LUZ-154613 session 2026-06-16"
tags: [mongodb, objectid, expr, index, performance, gotcha, luz-docs]
---

# MongoDB $expr + $toObjectId for _id range is correct but does not use the _id index (full scan)

Wrapping an _id range as `{$expr:{$and:[{$gte:["$_id",{$toObjectId:"<hex>"}]},{$lt:[...]}]}}` makes the count CORRECT (ObjectId-vs-ObjectId comparison) but does NOT use the _id index — it full-scans. Proven on luz-docs LUZ-154613 K=4 fan-out over 128k docs: each of the 4 concurrent sub-counts took ~9.1s INCLUDING the three buckets that returned 0 docs (an indexed empty-range count would be instant) → every sub-count scanned the whole collection. K=4 wall 13.3s vs single indexed count ~3.5s → the fan-out was SLOWER than the thing it optimizes.

Why: $expr aggregation-expression comparisons here don't get the index bounds a native typed range (`{_id:{$gte:ObjectId(..),$lt:ObjectId(..)}}`) would. The luz_jsonstore gateway only coerces a hex _id string to ObjectId for equality/$in, not inside $gte/$lt, so the index-using native form can't be expressed from the client today.

Consequence for the divide-and-conquer count: through the gateway you can have correct ($expr, scan) OR fast (native indexed range) but not both — the index-seek premise (DESIGN §5) needs a gateway change to coerce range bounds to BSON ObjectId (or parse {"$oid":..} into a native range). Also observed: synthetic ids cluster, so uniform hex boundaries put 100% in one bucket (load skew, DESIGN §8) — quantile boundaries fix balance but NOT the $expr-scan problem (a scan reads all docs regardless of range).

## Related

- [[Mongo _id range with hex-string bounds matches nothing unless gateway coerces to ObjectId]]
- [[1 Projects/luz-docs/count/optimize/Divide-and-Conquer Visible-Document Count]]
