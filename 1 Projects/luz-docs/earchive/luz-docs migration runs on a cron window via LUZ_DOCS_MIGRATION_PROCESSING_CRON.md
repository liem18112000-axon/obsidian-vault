---
title: "luz-docs migration runs on a cron window via LUZ_DOCS_MIGRATION_PROCESSING_CRON"
created: 2026-06-11
type: concept
status: seedling
source: "session 2026-06-11"
tags: [luz-docs, earchive, migration, cron, configmap]
---

# luz-docs migration runs on a cron window via LUZ_DOCS_MIGRATION_PROCESSING_CRON

The luz-docs eArchive migration (backfill) is gated by two properties in `kubernetes-overlays/env-<env>/luz-docs/system.properties` (luz_kubernetes repo):

- `LUZ_DOCS_MIGRATION_TENANT_IDS` — tenant allowlist, `*` = all tenants.
- `LUZ_DOCS_MIGRATION_PROCESSING_CRON` — cron expression for when migration processing may run. Dev default is the off-peak night window `* 20-23,1-5 * * *` (every minute during 20:00-23:59 and 01:00-05:59); set `* * * * *` to let it run continuously, e.g. while testing the backfill on dev.

Related pubsub plumbing lives next to them under the `# Migration` block (`LUZ_DOCS_MIGRATION_PUBSUB_IDLE_QUEUE_*`, `..._ACTIVE_QUEUE_*`).

## Related

- [[LUZ_DOCS_TENANTS_USE_MATERIALIZED gates the materialised read path per tenant]]
