---
ai_hash: ddd001cb8a0ae012
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-21
entities:
- Campaign flag
- materialize write path
- isAllowedTenant guard
- LUZ-156856
- switch-flag-for-materialize branch
- '2026-07-21'
- per-tenant materialize guard (old)
- luz.docs.tenants.use-materialized allowlist
- commit 38a07438d
- migration-campaign config
- MaterializeFacade.isAllowedTenant(tenantId)
- Campaign.of(...).isEnabled()
- isAffectedFor(tenantId)
- LUZ_DOCS_MIGRATION_CAMPAIGN_<SUBJECT>_ENABLED
- _INCLUDE_TENANT_IDS
- _EXCLUDE_TENANT_IDS
- backfill scheduling
- write-path stamping
- cascades
- completion gate
- shouldUseMaterialized
- read path
- Guard sites
- DocumentCreatingService
- DocumentService
- FolderService
- MaterializeResponseFilter
- unconditional stamping
- sentinels
- half-materialized data
- PropertyRetriever
- old allowlist bean
- luz-docs migration campaign per-tenant activation flow
source: session 2026-07-21 LUZ-156856
status: seedling
tags:
- luz-docs
- materialize
- migration
- design-decision
title: Campaign flag now guards materialize write path (isAllowedTenant)
type: observation
---

# Campaign flag now guards materialize write path (isAllowedTenant)

Decision (LUZ-156856, branch switch-flag-for-materialize, 2026-07-21): the per-tenant materialize guard removed with the old `luz.docs.tenants.use-materialized` allowlist (commit 38a07438d made stamping unconditional) was restored — but backed by the migration-campaign config instead of a second, drift-prone list.

`MaterializeFacade.isAllowedTenant(tenantId)` = `Campaign.of(MATERIALIZE_...).isEnabled() && isAffectedFor(tenantId)`, i.e. `LUZ_DOCS_MIGRATION_CAMPAIGN_<SUBJECT>_ENABLED` + `_INCLUDE_TENANT_IDS` / `_EXCLUDE_TENANT_IDS`. One config now drives all three concerns for a tenant: backfill scheduling, write-path stamping/cascades, and (ANDed with the completion gate in `shouldUseMaterialized`) the read path.

Guard sites = exactly the pre-38a0 ones: facade `should*` + `tryMaterializeBulkPatch`, create/patch stamping in DocumentCreatingService/DocumentService, folder recover + delete-subtree cascades in FolderService, retry-sweep seeding in MaterializeResponseFilter.

Why: unconditional stamping wrote sentinels for tenants whose backfill campaign was never scheduled (excluded/disabled), creating half-materialized data with no owner; keying the guard on the campaign keeps rollout membership in ONE place.

Trade-off vs old allowlist bean: evaluated per call via PropertyRetriever (env lookup) instead of parsed once at startup — config changes apply on restart either way, cost negligible.

Related: [[luz-docs migration campaign per-tenant activation flow]].

## Related

- [[luz-docs migration campaign per-tenant activation flow]]

%% ai-graph-start %%

**Related notes:**
- [[Materialize tenant allowlist removed - cascade unconditional]]
- [[luz-docs migration campaign per-tenant activation flow]]
- [[Campaign-gate template cache then campaign status L1 then repository L2]]
- [[LUZ_DOCS_TENANTS_USE_MATERIALIZED gates the materialised read path per tenant]]
- [[Campaign COMPLETED status is only trusted after re-verifying document state (truth-check gate)]]

**Relations:**
- Campaign flag — *guards* — materialize write path
- Campaign flag — *implements* — isAllowedTenant guard
- LUZ-156856 — *is a decision for* — Campaign flag
- LUZ-156856 — *made on* — 2026-07-21
- LUZ-156856 — *involved branch* — switch-flag-for-materialize branch
- per-tenant materialize guard (old) — *removed with* — luz.docs.tenants.use-materialized allowlist
- commit 38a07438d — *made* — unconditional stamping
- isAllowedTenant guard — *restored with* — migration-campaign config
- MaterializeFacade.isAllowedTenant(tenantId) — *uses* — Campaign.of(...).isEnabled()
- MaterializeFacade.isAllowedTenant(tenantId) — *uses* — isAffectedFor(tenantId)
- migration-campaign config — *uses* — LUZ_DOCS_MIGRATION_CAMPAIGN_<SUBJECT>_ENABLED
- migration-campaign config — *uses* — _INCLUDE_TENANT_IDS
- migration-campaign config — *uses* — _EXCLUDE_TENANT_IDS
- migration-campaign config — *drives* — backfill scheduling
- migration-campaign config — *drives* — write-path stamping
- migration-campaign config — *drives* — cascades
- migration-campaign config — *drives* — read path
- read path — *ANDed with* — completion gate
- completion gate — *in* — shouldUseMaterialized
- Guard sites — *include* — MaterializeFacade.isAllowedTenant(tenantId)
- Guard sites — *include* — DocumentCreatingService
- Guard sites — *include* — DocumentService
- Guard sites — *include* — FolderService
- Guard sites — *include* — MaterializeResponseFilter
- unconditional stamping — *created* — sentinels
- unconditional stamping — *created* — half-materialized data
- migration-campaign config — *evaluated by* — PropertyRetriever
- old allowlist bean — *evaluated* — at startup
- luz-docs migration campaign per-tenant activation flow — *related to* — migration-campaign config

%% ai-graph-end %%