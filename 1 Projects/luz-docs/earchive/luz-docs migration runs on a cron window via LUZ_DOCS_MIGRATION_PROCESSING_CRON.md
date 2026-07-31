---
ai_hash: 7c2a2c45c099ad5b
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-11
entities:
- luz-docs migration
- LUZ_DOCS_MIGRATION_PROCESSING_CRON
- luz-docs eArchive migration
- kubernetes-overlays/env-<env>/luz-docs/system.properties
- luz_kubernetes repo
- LUZ_DOCS_MIGRATION_TENANT_IDS
- tenant allowlist
- cron expression
- off-peak night window
- pubsub plumbing
- '# Migration block'
- LUZ_DOCS_MIGRATION_PUBSUB_IDLE_QUEUE_*
- LUZ_DOCS_MIGRATION_PUBSUB_ACTIVE_QUEUE_*
- LUZ_DOCS_TENANTS_USE_MATERIALIZED
- materialised read path
source: session 2026-06-11
status: seedling
tags:
- luz-docs
- earchive
- migration
- cron
- configmap
title: luz-docs migration runs on a cron window via LUZ_DOCS_MIGRATION_PROCESSING_CRON
type: concept
---

# luz-docs migration runs on a cron window via LUZ_DOCS_MIGRATION_PROCESSING_CRON

The luz-docs eArchive migration (backfill) is gated by two properties in `kubernetes-overlays/env-<env>/luz-docs/system.properties` (luz_kubernetes repo):

- `LUZ_DOCS_MIGRATION_TENANT_IDS` — tenant allowlist, `*` = all tenants.
- `LUZ_DOCS_MIGRATION_PROCESSING_CRON` — cron expression for when migration processing may run. Dev default is the off-peak night window `* 20-23,1-5 * * *` (every minute during 20:00-23:59 and 01:00-05:59); set `* * * * *` to let it run continuously, e.g. while testing the backfill on dev.

Related pubsub plumbing lives next to them under the `# Migration` block (`LUZ_DOCS_MIGRATION_PUBSUB_IDLE_QUEUE_*`, `..._ACTIVE_QUEUE_*`).

## Related

- [[LUZ_DOCS_TENANTS_USE_MATERIALIZED gates the materialised read path per tenant]]

%% ai-graph-start %%

**Related notes:**
- [[luz-docs migration campaign per-tenant activation flow]]
- [[luz_docs migration campaigns retry on next tenant request, not cron]]
- [[LUZ_DOCS_TENANTS_USE_MATERIALIZED gates the materialised read path per tenant]]
- [[Campaign-gate template cache then campaign status L1 then repository L2]]
- [[Campaign flag now guards materialize write path (isAllowedTenant)]]

**Relations:**
- luz-docs migration — *runs via* — LUZ_DOCS_MIGRATION_PROCESSING_CRON
- luz-docs eArchive migration — *is gated by* — LUZ_DOCS_MIGRATION_TENANT_IDS
- luz-docs eArchive migration — *is gated by* — LUZ_DOCS_MIGRATION_PROCESSING_CRON
- LUZ_DOCS_MIGRATION_TENANT_IDS — *is a property in* — kubernetes-overlays/env-<env>/luz-docs/system.properties
- LUZ_DOCS_MIGRATION_PROCESSING_CRON — *is a property in* — kubernetes-overlays/env-<env>/luz-docs/system.properties
- kubernetes-overlays/env-<env>/luz-docs/system.properties — *is located in* — luz_kubernetes repo
- LUZ_DOCS_MIGRATION_TENANT_IDS — *is a* — tenant allowlist
- LUZ_DOCS_MIGRATION_PROCESSING_CRON — *is a* — cron expression
- cron expression — *has Dev default* — off-peak night window
- pubsub plumbing — *lives under* — # Migration block
- # Migration block — *contains* — LUZ_DOCS_MIGRATION_PUBSUB_IDLE_QUEUE_*
- # Migration block — *contains* — LUZ_DOCS_MIGRATION_PUBSUB_ACTIVE_QUEUE_*
- LUZ_DOCS_TENANTS_USE_MATERIALIZED — *gates* — materialised read path

%% ai-graph-end %%