---
ai_hash: de7f71d885eb47ad
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-21
entities:
- luz-docs migration campaign
- MATERIALIZE_DOCUMENTS_AND_FOLDERS_SECURITY_CLASS_CODES
- Campaign.isEnabled()
- Campaign.isAffectedFor(tenantId)
- LUZ_DOCS_MIGRATION_CAMPAIGN_<SUBJECT>_ENABLED
- LUZ_DOCS_MIGRATION_CAMPAIGN_<SUBJECT>_INCLUDE_TENANT_IDS
- LUZ_DOCS_MIGRATION_CAMPAIGN_<SUBJECT>_EXCLUDE_TENANT_IDS
- MigrationEventTrigger
- JAX-RS filter
- tenant API request
- async event
- MigrationEventOrchestrator
- luz_docs_migration_campaign_<subject>_scheduled
- DualCache
- idle pubsub queue
- Invocation
- campaign doc
- luz_docs_migration_campaign collection
- active queue
- MigrationMessageReceiver
- LUZ_DOCS_MIGRATION_CRONTAB
- Catalogue executor bean
- Read path
- MaterializeGate L1
- Catalogue version
- luz.docs.tenants.use-materialized allowlist
- luz_docs 38a07438d
- backfill scheduling
- campaign completion
- reads switch
- Hand-rolled Optional.or fallback chain replaces CDI @Fallback
source: trace session 2026-07-21, commit 38a07438d
status: seedling
tags:
- luz-docs
- migration
- pubsub
- rollout
title: luz-docs migration campaign per-tenant activation flow
type: concept
---

# luz-docs migration campaign per-tenant activation flow

Per-tenant activation of a luz-docs migration campaign (e.g. MATERIALIZE_DOCUMENTS_AND_FOLDERS_SECURITY_CLASS_CODES) is driven by three env properties, evaluated in `Campaign.isEnabled()` / `isAffectedFor(tenantId)`:

- `LUZ_DOCS_MIGRATION_CAMPAIGN_<SUBJECT>_ENABLED` = true (per-campaign kill switch)
- `LUZ_DOCS_MIGRATION_CAMPAIGN_<SUBJECT>_INCLUDE_TENANT_IDS` = `*` or CSV (blank/missing = nobody)
- `LUZ_DOCS_MIGRATION_CAMPAIGN_<SUBJECT>_EXCLUDE_TENANT_IDS` (exclude wins)

Pipeline: **every tenant API request** passes `MigrationEventTrigger` (JAX-RS filter) → enabled+affected campaigns fire an async event → `MigrationEventOrchestrator` dedups per tenant+campaign for 24h (`luz_docs_migration_campaign_<subject>_scheduled` DualCache key — this is the "retry within 24h on next tenant request" behavior) and publishes to an **idle** pubsub queue → a separate Invocation drains idle, upserts the campaign doc in tenant-DB collection `luz_docs_migration_campaign` (`findOrCreate`), marks INCOMPLETE, publishes to the **active** queue → `MigrationMessageReceiver` (1 thread, 1 outstanding msg, curfew via `LUZ_DOCS_MIGRATION_CRONTAB`, 8h overlapping-guard locks) sets IN_PROGRESS + attempt++, runs the Catalogue executor bean, writes back COMPLETED/INCOMPLETE.

Read path only flips when the doc says COMPLETED (MaterializeGate L1) — the rollout is self-verifying. Bumping the `Catalogue` version re-runs a COMPLETED campaign (`isUpToDate`).

This replaced the old static `luz.docs.tenants.use-materialized` allowlist (removed in luz_docs 38a07438d): stamping is now unconditional on the write path; INCLUDE/EXCLUDE lists control who gets the backfill *scheduled*, and campaign completion controls when reads switch.

Related: [[Hand-rolled Optional.or fallback chain replaces CDI @Fallback]].

## Related

- [[Hand-rolled Optional.or fallback chain replaces CDI @Fallback]]

%% ai-graph-start %%

**Related notes:**
- [[Campaign flag now guards materialize write path (isAllowedTenant)]]
- [[Campaign-gate template cache then campaign status L1 then repository L2]]
- [[luz_docs migration campaigns retry on next tenant request, not cron]]
- [[Materialize tenant allowlist removed - cascade unconditional]]
- [[Campaign COMPLETED status is only trusted after re-verifying document state (truth-check gate)]]

**Relations:**
- luz-docs migration campaign — *is activated by* — LUZ_DOCS_MIGRATION_CAMPAIGN_<SUBJECT>_ENABLED
- luz-docs migration campaign — *is activated by* — LUZ_DOCS_MIGRATION_CAMPAIGN_<SUBJECT>_INCLUDE_TENANT_IDS
- luz-docs migration campaign — *is activated by* — LUZ_DOCS_MIGRATION_CAMPAIGN_<SUBJECT>_EXCLUDE_TENANT_IDS
- MATERIALIZE_DOCUMENTS_AND_FOLDERS_SECURITY_CLASS_CODES — *is a type of* — luz-docs migration campaign
- LUZ_DOCS_MIGRATION_CAMPAIGN_<SUBJECT>_ENABLED — *is evaluated in* — Campaign.isEnabled()
- LUZ_DOCS_MIGRATION_CAMPAIGN_<SUBJECT>_INCLUDE_TENANT_IDS — *is evaluated in* — Campaign.isAffectedFor(tenantId)
- LUZ_DOCS_MIGRATION_CAMPAIGN_<SUBJECT>_EXCLUDE_TENANT_IDS — *is evaluated in* — Campaign.isAffectedFor(tenantId)
- MigrationEventTrigger — *is a* — JAX-RS filter
- MigrationEventTrigger — *processes* — tenant API request
- MigrationEventTrigger — *fires* — async event
- async event — *is handled by* — MigrationEventOrchestrator
- MigrationEventOrchestrator — *dedups using* — luz_docs_migration_campaign_<SUBJECT>_scheduled
- luz_docs_migration_campaign_<SUBJECT>_scheduled — *is a key in* — DualCache
- MigrationEventOrchestrator — *publishes to* — idle pubsub queue
- Invocation — *drains* — idle pubsub queue
- Invocation — *upserts* — campaign doc
- campaign doc — *is stored in* — luz_docs_migration_campaign collection
- Invocation — *publishes to* — active queue
- MigrationMessageReceiver — *drains* — active queue
- MigrationMessageReceiver — *runs* — Catalogue executor bean
- MigrationMessageReceiver — *enforces curfew via* — LUZ_DOCS_MIGRATION_CRONTAB
- Read path — *flips when* — campaign doc
- campaign doc — *is COMPLETED for* — Read path
- MaterializeGate L1 — *is part of* — Read path
- Bumping Catalogue version — *re-runs* — luz-docs migration campaign
- luz-docs migration campaign — *replaced* — luz.docs.tenants.use-materialized allowlist
- luz.docs.tenants.use-materialized allowlist — *was removed in* — luz_docs 38a07438d
- LUZ_DOCS_MIGRATION_CAMPAIGN_<SUBJECT>_INCLUDE_TENANT_IDS — *controls* — backfill scheduling
- LUZ_DOCS_MIGRATION_CAMPAIGN_<SUBJECT>_EXCLUDE_TENANT_IDS — *controls* — backfill scheduling
- campaign completion — *controls* — reads switch
- luz-docs migration campaign — *is related to* — Hand-rolled Optional.or fallback chain replaces CDI @Fallback

%% ai-graph-end %%