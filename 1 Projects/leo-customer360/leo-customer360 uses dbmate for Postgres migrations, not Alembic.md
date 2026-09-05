---
title: "leo-customer360 uses dbmate for Postgres migrations, not Alembic"
created: 2026-08-24
type: argument
status: seedling
source: "session 2026-08-24 migration tooling research"
tags: [leo-customer360, postgres, migrations, dbmate, decision]
---

# leo-customer360 uses dbmate for Postgres migrations, not Alembic

For `leo-customer360` we run PostgreSQL schema migrations with **dbmate** (a single static Go binary, run-and-exit, plain `.sql` up/down files) plus **Atlas `migrate lint` in CI only** as a destructive-change/lock guardrail. This holds even though every service is Python/FastAPI + SQLAlchemy 2.0 — because SQLAlchemy is used for *queries*, and the schema itself is **raw SQL** (a hand-written `database-init/database-schema.sql` with RLS tenant policies, PostGIS, pgvector, `DO $$` blocks, custom `customer360` schema).

**Why dbmate:** raw-SQL migrations run verbatim (no DDL subset to fight), it maps onto the `NNN_*.sql` convention already started in the repo, and its ~8 MB footprint fits the VNG Cloud VM least-RAM/CPU goal.

**Why NOT Alembic** (the obvious Python choice, deliberately declined): its value is `--autogenerate` diffing SQLAlchemy *ORM models* — but our schema isn't in ORM models, and Alembic can't model RLS / PostGIS / pgvector / `CREATE EXTENSION` / DO-blocks. You'd write all of it as `op.execute("<raw sql>")` inside Python files — paying Python+Alembic overhead to run raw SQL, strictly worse than dbmate. Revisit only if the schema ever moves into ORM models.

**Why NOT Flyway/Liquibase:** JVM (200–400 MB RAM/run, ~300 MB image) contradicts the VNG footprint goal; their best features are paid tiers anyway.

**Deploy target:** the project runs on **Docker Compose today** (not K8s), so dbmate runs as a one-shot `migrate` Compose service gated by `depends_on: { migrate: { condition: service_completed_successfully } }` before the API. The same image runs as a k8s `Job`/initContainer *if* we later move to VKS — migration files don't change.

The full research write-up lives at `docs/research-tech/sql-migration-tool-recommendation.md`. Core problem it solves: see [[Postgres docker-entrypoint-initdb.d runs only once on an empty volume]].

## Related

- [[Postgres docker-entrypoint-initdb.d runs only once on an empty volume]]
