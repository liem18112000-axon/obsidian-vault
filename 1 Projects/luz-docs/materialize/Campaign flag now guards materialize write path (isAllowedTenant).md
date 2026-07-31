---
title: "Campaign flag now guards materialize write path (isAllowedTenant)"
created: 2026-07-21
type: observation
status: seedling
source: "session 2026-07-21 LUZ-156856"
tags: [luz-docs, materialize, migration, design-decision]
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
