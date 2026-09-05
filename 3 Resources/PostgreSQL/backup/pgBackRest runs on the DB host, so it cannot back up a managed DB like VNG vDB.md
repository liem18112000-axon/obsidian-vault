---
title: "pgBackRest runs on the DB host, so it cannot back up a managed DB like VNG vDB"
created: 2026-08-16
type: lesson
status: seedling
source: "session 2026-08-16 physical track"
tags: [pgbackrest, postgres, backup, pitr, gotcha]
---

# pgBackRest runs on the DB host, so it cannot back up a managed DB like VNG vDB

pgBackRest performs a **physical** backup: it reads the PostgreSQL data directory (`PGDATA`) directly and opens a local control connection (unix socket) to call `pg_backup_start`/`pg_backup_stop`. Therefore it must run **on the same host/pod as the database** — it cannot back up over a plain TCP libpq connection from elsewhere (remote operation only via its own SSH/TLS repo-host mode).

**Consequence:** a fully **managed** database (VNG Cloud vDB, RDS, Cloud SQL, ...) gives you no filesystem or superuser/socket access, so **you cannot run pgBackRest against it**. Physical/PITR there is whatever the provider offers (for VNG vDB: Backup Center snapshots). pgBackRest is for **self-managed** Postgres only — Docker/VM and hand-rolled K8s StatefulSets.

In LEO CDP Customer360 this splits the backup story: Docker + self-managed K8s use pgBackRest; managed vDB uses [[VNG vDB PostgreSQL cluster has no Terraform backup args (standalone-only)|Backup Center]] + a logical pg_dump CronJob. Related: [[pgBackRest archive_command needs the binary IN the postgres image; the scheduler is a separate sidecar]].

## Related

- [[pgBackRest archive_command needs the binary IN the postgres image; the scheduler is a separate sidecar]]
