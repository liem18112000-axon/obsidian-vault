---
ai_hash: 3bc3b7ea7864f5a8
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-14
entities: []
source: accesstrade deployment/ Terraform, 2026-06-14
status: seedling
tags:
- gcp
- cloud-sql
- postgres
- terraform
- backup
- gotcha
title: Cloud SQL Postgres point-in-time recovery requires automated backups enabled
type: lesson
---

# Cloud SQL Postgres point-in-time recovery requires automated backups enabled

Cloud SQL for PostgreSQL point-in-time recovery (`backup_configuration.point_in_time_recovery_enabled = true`) is backed by **WAL archiving**, which only runs when automated backups are on. So PITR is **only valid when `backup_configuration.enabled = true`** — enabling PITR while backups are disabled is a config error, not a silent no-op.

Practical Terraform consequence: gate both on the same variable (e.g. `point_in_time_recovery_enabled = var.db_backup_enabled`) so you can never produce the invalid combination.

Seen provisioning the accesstrade_integration `deployment/` Terraform (project klara-nonprod).

## Related
[[Cloud SQL ssl_mode replaced the require_ssl boolean in the hashicorp google provider]]

## Related

- [[Cloud SQL ssl_mode replaced the require_ssl boolean in the hashicorp google provider]]

%% ai-graph-start %%

**Related notes:**
- [[Cloud SQL ssl_mode replaced the require_ssl boolean in the hashicorp google provider]]
- [[Cloud SQL ENTERPRISE_PLUS edition rejects shared-core tiers like db-f1-micro]]
- [[Memorystore Redis is always VPC-internal — no public endpoint]]

%% ai-graph-end %%