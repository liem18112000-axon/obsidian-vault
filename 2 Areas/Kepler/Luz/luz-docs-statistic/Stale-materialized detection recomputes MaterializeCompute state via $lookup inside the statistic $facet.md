---
ai_hash: d93c1b4c8acd32f3
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-11
entities:
- Stale-materialized detection
- MaterializeCompute state
- $lookup
- statistic $facet
- luz_docs_statistic
- staleMaterializedDocuments facet
- sprint 158
- LUZ-155460
- docs
- materialize sentinels
- luz_docs
- MaterializeCompute.compute()
- read-side aggregation
- folders
- _id
- folderIds
- strings
- ObjectId
- $toString
- $toObjectId
- malformed ids
- Per-folderIds-position slots
- $map
- $filter
- public folder
- _effectiveSecurityClassCodes
- _folderSecurityClassCodes
- $setEquals
- LinkedHashSet
- $setUnion
- _folderNames
- $ne
- update-with-pipeline
- Mongo WriteError 72
- luz_docs cascades
- prefetched folder-code literals
- modern MongoDB
- pipeline
- JSON text block
- materialize.MaterializeQueryBuilder.STALE_MATERIALIZED_RECOMPUTE_STAGES
- JsonObjectBuilder
- $group count+size stage
- shared builder
- luz_docs_statistic unmaterializedDocuments metric
- dev
- '2026-06-11'
- commit 883a65c
- jsonstore
- aggregate endpoint
- tenants
- canary tenant
- service
- reference design
- user
- missing folder
- stored order
- recomputed order
- every-minute job
- note
source: LUZ-155460 implementation, session 2026-06-11
status: seedling
tags:
- luz
- luz-docs-statistic
- materialize
- mongodb
- aggregation
- LUZ-155460
title: Stale-materialized detection recomputes MaterializeCompute state via $lookup
  inside the statistic $facet
type: model
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

%% ai-graph-start %%

**Related notes:**
- [[luz_docs_statistic unmaterializedDocuments metric counts docs missing any materialize sentinel field]]
- [[luz_docs_statistic computes per-tenant unmaterializedDocuments count]]
- [[luz_docs bulk updateMany recompute is set-based - one event, batched literal-table pipeline, not per-doc fan-out]]
- [[luz_docs parent-change cascade pipeline rebuilds _folderSecurityClassCodes positionally then re-derives the sentinels]]
- [[Missing folder reference produces fail-closed materialize state]]

**Relations:**
- Stale-materialized detection — *recomputes* — MaterializeCompute state
- Stale-materialized detection — *uses* — $lookup
- Stale-materialized detection — *uses* — statistic $facet
- $lookup — *is_inside* — statistic $facet
- luz_docs_statistic — *has_facet* — staleMaterializedDocuments facet
- staleMaterializedDocuments facet — *finds* — docs
- staleMaterializedDocuments facet — *developed_during* — sprint 158
- staleMaterializedDocuments facet — *addresses_issue* — LUZ-155460
- docs — *have* — materialize sentinels
- materialize sentinels — *do_not_match* — MaterializeCompute.compute() output
- MaterializeCompute.compute() — *part_of* — luz_docs
- staleMaterializedDocuments facet — *re-derives* — MaterializeCompute state
- MaterializeCompute state — *derived_in* — read-side aggregation
- $lookup — *joins* — folders
- $lookup — *joins_with* — docs
- $lookup — *uses_field_from* — folders._id
- $lookup — *uses_field_from* — docs.folderIds
- folderIds — *has_type* — strings
- _id — *has_type* — ObjectId
- $toString — *avoids* — $toObjectId
- $toObjectId — *fails_on* — malformed ids
- Per-folderIds-position slots — *rebuilt_using* — $map
- Per-folderIds-position slots — *rebuilt_using* — $filter
- missing folder — *is_not_a* — public folder
- Set-valued comparisons — *use* — $setEquals
- Set-valued comparisons — *compare* — _effectiveSecurityClassCodes
- Set-valued comparisons — *compare* — _folderSecurityClassCodes
- $setEquals — *is* — order-insensitive
- stored order — *is* — LinkedHashSet insertion order
- recomputed order — *is* — $setUnion's
- _folderNames — *is_a* — ordered parallel array
- _folderNames — *compared_with* — $ne
- $lookup — *is_forbidden_in* — update-with-pipeline
- update-with-pipeline — *causes* — Mongo WriteError 72
- luz_docs cascades — *inline* — prefetched folder-code literals
- $lookup — *is_fine_inside* — $facet
- $lookup — *is_fine_on* — modern MongoDB
- pipeline — *is* — static
- pipeline — *stored_as_type* — JSON text block
- JSON text block — *located_in* — materialize.MaterializeQueryBuilder.STALE_MATERIALIZED_RECOMPUTE_STAGES
- materialize.MaterializeQueryBuilder.STALE_MATERIALIZED_RECOMPUTE_STAGES — *parsed_how* — once
- $group count+size stage — *appended_by_component* — shared builder
- staleMaterializedDocuments facet — *related_to* — luz_docs_statistic unmaterializedDocuments metric
- luz_docs_statistic unmaterializedDocuments metric — *counts* — docs missing any materialize sentinel field
- Stale-materialized detection — *verified_on* — dev
- Stale-materialized detection — *verified_on_date* — 2026-06-11
- Stale-materialized detection — *shipped_as* — commit 883a65c
- jsonstore aggregate endpoint — *supports* — $lookup-inside-$facet
- every-minute job — *processes* — tenants
- every-minute job — *processes* — canary tenant
- canary tenant — *contains* — 128k-document
- user — *removed* — staleMaterializedDocuments facet
- staleMaterializedDocuments facet — *removed_from* — luz_docs_statistic
- staleMaterializedDocuments facet — *removed_on* — 2026-06-11
- service — *no_longer_computes* — staleMaterializedDocuments facet
- note — *is_a* — reference design
- reference design — *for* — Stale-materialized detection
- Stale-materialized detection — *proven_via* — jsonstore

%% ai-graph-end %%