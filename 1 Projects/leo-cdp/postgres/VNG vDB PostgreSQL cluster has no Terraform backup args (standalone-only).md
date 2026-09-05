---
title: "VNG vDB PostgreSQL cluster has no Terraform backup args (standalone-only)"
created: 2026-08-16
type: lesson
status: seedling
source: "session 2026-08-16 postgres/docs research"
tags: [vngcloud, postgres, backup, terraform, gotcha, leo-cdp]
---

# VNG vDB PostgreSQL cluster has no Terraform backup args (standalone-only)

The VNG Cloud vDB Terraform resource `vngcloud_vdb_postgresql_cluster` (HA topology) exposes **no** `backup_auto` / `backup_duration` / `backup_time` arguments. Those exist **only** on the standalone `vngcloud_vdb_relational_database`. A cluster instead takes `backup_policy_id` + `backup_location_id`, which reference a Backup Policy/Location defined in VNG **Backup Center**, plus `backup_id` to restore-from.

**Why it matters:** in the LEO CDP Customer360 repo, prod uses `pg_topology = "cluster"`, so any `backup_auto`/`backup_duration` set in the postgres module variables silently do nothing for prod. Prod backups must be wired via Backup Center IDs, and supplemented with a logical `pg_dump` CronJob to vStorage.

Also: vDB **PITR is not documented** (snapshot-based only), restore always **creates a new instance/cluster**, and the 2–14 day retention range is the standalone limit (verify cluster policy range in console). pgvector is available on vDB only for instances created ≥ 2024-08-01.

See [[pgvector logical restore rebuilds HNSW-IVFFlat indexes; physical backup copies them as-is]].

## Related

- [[pgvector logical restore rebuilds HNSW-IVFFlat indexes; physical backup copies them as-is]]
