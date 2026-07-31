---
title: "Stale-materialized detection recomputes MaterializeCompute state via $lookup inside the statistic $facet"
created: 2026-06-11
type: model
status: seedling
source: "LUZ-155460 implementation, session 2026-06-11"
tags: [luz, luz-docs-statistic, materialize, mongodb, aggregation, LUZ-155460]
---

# Stale-materialized detection recomputes MaterializeCompute state via $lookup inside the statistic $facet

luz_docs_statistic's `staleMaterializedDocuments` facet (sprint 158, LUZ-155460) finds docs whose 4 materialize sentinels exist but no longer match what luz_docs' `MaterializeCompute.compute()` would produce. It re-derives the state inside the read-side aggregation and compares:

- `$lookup` from `folders` joins on `{$toString: "$_id"}` vs the doc's `folderIds` — folderIds are **strings**, folders._id is an **ObjectId**; $toString avoids $toObjectId blowing up on malformed ids.
- Per-folderIds-position slots are rebuilt via $map+$filter so order follows folderIds (the $lookup result order is unspecified); a missing folder yields {name:"", codes:[]} and does NOT count as a public folder — exactly compute()'s semantics.
- Set-valued comparisons (`_effectiveSecurityClassCodes`, per-slot `_folderSecurityClassCodes`) use **$setEquals** (order-insensitive — stored order is LinkedHashSet insertion order, recomputed order is $setUnion's); `_folderNames` is an ordered parallel array, compared positionally with $ne.
- Why the read side can $lookup but the luz_docs cascades cannot: $lookup is forbidden in update-with-pipeline (Mongo WriteError 72) — cascades inline prefetched folder-code literals instead. $lookup inside $facet is fine on modern MongoDB.
- The pipeline is static, so it lives as a JSON text block in `materialize.MaterializeQueryBuilder.STALE_MATERIALIZED_RECOMPUTE_STAGES` (parsed once) instead of ~200 lines of JsonObjectBuilder code; the $group count+size stage is appended by the shared builder.

Related: [[luz_docs_statistic unmaterializedDocuments metric counts docs missing any materialize sentinel field]]

## Related

- [[luz_docs_statistic unmaterializedDocuments metric counts docs missing any materialize sentinel field]]

> [!success] Verified on dev (2026-06-11)
> Shipped as commit 883a65c. jsonstore's aggregate endpoint passes the $lookup-inside-$facet through fine — the every-minute job processed multiple tenants with zero errors, including the 128k-document canary tenant (~1s per facet aggregation). The "needs dev verification" caveat is resolved.

> [!warning] Removed (2026-06-11, same day)
> The user removed the staleMaterializedDocuments metric from luz_docs_statistic right after the verified rollout — the service no longer computes it. This note stays as the reference design for $lookup-based stale detection (the technique is proven to work through jsonstore) in case it's revived.
