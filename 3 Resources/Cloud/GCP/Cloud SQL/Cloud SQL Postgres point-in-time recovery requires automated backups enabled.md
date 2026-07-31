---
title: "Cloud SQL Postgres point-in-time recovery requires automated backups enabled"
created: 2026-06-14
type: lesson
status: seedling
source: "accesstrade deployment/ Terraform, 2026-06-14"
tags: [gcp, cloud-sql, postgres, terraform, backup, gotcha]
---

# Cloud SQL Postgres point-in-time recovery requires automated backups enabled

Cloud SQL for PostgreSQL point-in-time recovery (`backup_configuration.point_in_time_recovery_enabled = true`) is backed by **WAL archiving**, which only runs when automated backups are on. So PITR is **only valid when `backup_configuration.enabled = true`** — enabling PITR while backups are disabled is a config error, not a silent no-op.

Practical Terraform consequence: gate both on the same variable (e.g. `point_in_time_recovery_enabled = var.db_backup_enabled`) so you can never produce the invalid combination.

Seen provisioning the accesstrade_integration `deployment/` Terraform (project klara-nonprod).

## Related
[[Cloud SQL ssl_mode replaced the require_ssl boolean in the hashicorp google provider]]

## Related

- [[Cloud SQL ssl_mode replaced the require_ssl boolean in the hashicorp google provider]]
