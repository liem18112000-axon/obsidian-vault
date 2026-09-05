---
title: "LEO CDP schema migrations are ordered plain SQL, not dbmate or alembic"
created: 2026-08-26
type: lesson
status: seedling
source: "leo-customer360 release-doc work, session 2026-08-26"
tags: [leo-cdp, postgres, migrations, gotcha]
---

# LEO CDP schema migrations are ordered plain SQL, not dbmate or alembic

LEO Customer 360 manages its PostgreSQL schema as **ordered plain SQL files**, applied in a fixed sequence by `deployments/postgres/run-sql.sh <uat|prod>` (over an SSH bastion, each with `ON_ERROR_STOP=1`): `deployments/postgres/**/*.sql` → `database-init/{database-schema, init-core-database, data-view-for-llm}.sql` → everything under `database-init/migrations/` alphabetically.

Gotcha: **`alembic` is a declared dependency in customer360-api but is NOT the migration mechanism** — there is no `alembic.ini` or versions tree, so `alembic upgrade` does nothing useful. A `dbmate` adoption was also tried and then **reverted**. Don't reach for either tool.

Rules that follow: new schema changes must be **additive, numbered files** under `database-init/migrations/` (e.g. `001_harden_tenant_rls_policies.sql`); never edit an already-applied file. Migrations are **forward-only** — there are no down-migrations, so plan changes backward-compatibly or recover from backup.

Locally the `postgres` image bakes schema into `/docker-entrypoint-initdb.d/`, which only runs on a fresh data volume (`./dev-c360.sh reset` to re-init).

## Related

- [[LEO CDP environment version drift: local PG16/Redis8 vs managed PG15/Redis7]]
