---
title: "Never pipe a dbmate migration file through a raw psql replay — its down section is destructive"
created: 2026-08-24
type: lesson
status: seedling
source: "session 2026-08-24 dbmate implementation"
tags: [dbmate, migrations, gotcha, prod-safety, leo-customer360]
---

# Never pipe a dbmate migration file through a raw psql replay — its down section is destructive

In the leo-customer360 repo, two schema-apply mechanisms coexist and are **mutually incompatible**:

- **dbmate** (docker-compose `migrate` service): applies `database-init/db/migrations/*.sql`, tracking versions in `schema_migrations`. Each file has a `-- migrate:up` and a `-- migrate:down` half; the baseline's down is `DROP SCHEMA IF EXISTS customer360 CASCADE;`.
- **`deployments/postgres/run-sql.sh`** (Terraform + bastion, UAT/prod): a **blind replay** that pipes every `*.sql` it finds (postgres/init, database-init/*.sql, database-init/migrations/*) straight into `psql -f -` over SSH. No version tracking; relies on IF NOT EXISTS / ON CONFLICT idempotency.

**The trap:** if `run-sql.sh` ever collects a *dbmate* migration file and pipes it to psql, psql runs the WHOLE file — including the `-- migrate:down` section. `-- migrate:down` is just a SQL comment to psql, so `DROP SCHEMA customer360 CASCADE` executes and **wipes the database**. dbmate only runs one half because it parses the markers; raw psql does not.

**Rules:**
- Never point a raw psql replay (run-sql.sh's `APP_SQL_DIR`/`MIGRATIONS_DIR`) at `database-init/db/migrations`.
- Migrating the prod/bastion path to dbmate means running the dbmate binary/container there (single static binary, installable like they install psql-client), NOT feeding migration files to psql.
- Until that follow-up lands, keep `database-init/migrations/001_*.sql` (old hand-rolled, safe to replay) so run-sql.sh still applies RLS hardening in prod.

Related: [[leo-customer360 uses dbmate for Postgres migrations, not Alembic]], [[dbmate treats any line starting with -- migrate:up/down as a directive]].

## Related

- [[leo-customer360 uses dbmate for Postgres migrations]]
- [[not Alembic]]
- [[dbmate treats any line starting with -- migrate:up/down as a directive]]
