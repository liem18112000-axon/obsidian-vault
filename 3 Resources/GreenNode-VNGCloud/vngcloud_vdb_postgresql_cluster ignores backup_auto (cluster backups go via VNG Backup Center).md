---
title: "vngcloud_vdb_postgresql_cluster ignores backup_auto (cluster backups go via VNG Backup Center)"
created: 2026-08-17
type: lesson
status: seedling
source: "session 2026-08-17"
tags: [greennode, vngcloud, vdb, terraform, backup, gotcha]
---

# vngcloud_vdb_postgresql_cluster ignores backup_auto (cluster backups go via VNG Backup Center)

On GreenNode/VNG Cloud vDB, the two PostgreSQL Terraform resources handle backups **differently**, and it is a silent trap:

- **Standalone** (`vngcloud_vdb_relational_database`) HAS first-class backup args: `backup_auto`, `backup_duration` (2-14 days), `backup_time`.
- **Cluster / HA** (`vngcloud_vdb_postgresql_cluster`, 1 writer + N readers) **does NOT accept those args**. Setting `backup_auto` on a cluster (or via a shared module variable) **silently does nothing** — no error, just no automatic backup.

For a cluster, backups are configured through **VNG Backup Center** and wired via `backup_policy_id` (schedule + retention) + `backup_location_id` on the cluster resource; `backup_id` restores/creates a cluster from a restore point. The 2-14 day retention range is the *standalone* limit — verify the cluster range in the console.

**Why it matters:** prod often uses the cluster topology for HA, so a module that just sets `backup_auto=true` leaves prod with NO automatic backups. Mitigate: set the Backup Center policy/location IDs AND add a logical `pg_dump` CronJob to vStorage as a second track. Also: restore = create a NEW instance/cluster from a restore point; auto backups are deleted with the instance, manual snapshots survive; no physical restore INTO vDB (logical dump/restore only) so migrations rebuild pgvector indexes.

Relates to [[Provision GreenNode/VNG Cloud vDB PostgreSQL with the vngcloud Terraform provider]].

## Related

- [[Provision GreenNode/VNG Cloud vDB PostgreSQL with the vngcloud Terraform provider]]
