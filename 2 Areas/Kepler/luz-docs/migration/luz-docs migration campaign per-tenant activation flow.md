---
title: "luz-docs migration campaign per-tenant activation flow"
created: 2026-07-21
type: concept
status: seedling
source: "trace session 2026-07-21, commit 38a07438d"
tags: [luz-docs, migration, pubsub, rollout]
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
