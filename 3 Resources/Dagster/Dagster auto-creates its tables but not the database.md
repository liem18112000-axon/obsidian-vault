---
title: "Dagster auto-creates its tables but not the database"
created: 2026-09-03
type: lesson
status: seedling
source: "leo-customer360 Phase 0, 2026-09-03"
tags: [dagster, postgres, kubernetes, deployment, gotcha]
---

# Dagster auto-creates its tables but not the database

When you point Dagster storage at PostgreSQL (`storage: postgres:` in `dagster.yaml`), Dagster **auto-creates its own tables** on first boot — but it does **not** create the target **database**. If the `db_name` does not already exist, startup fails to connect. So the database must be provisioned out-of-band:

- **k8s:** an idempotent init container (`psql -d postgres -tAc "SELECT 1 FROM pg_database WHERE datname=..." || CREATE DATABASE ...`) before the Dagster container.
- **managed / VM:** run `CREATE DATABASE dagster` once. The connecting user needs **CREATEDB**.

Use a **dedicated `dagster` database** (not the app DB) so Dagster tables never co-mingle with application schema; reuse the apps `DB_HOST/PORT/USER/PASSWORD` env and only fix the DB name.

Related deployment gotcha: **a k8s PVC (or any volume) mounted at `DAGSTER_HOME` shadows an image-baked `dagster.yaml`** at that path — the instance config silently reverts to defaults (SQLite). Once storage is external Postgres, drop the PVC (or mount the config via a ConfigMap `subPath`) so the baked instance config is actually used. Dropping the single-writer SQLite RWO PVC is also the prerequisite for running more than one Dagster process.

## Related
[[Scaling Dagster needs Postgres storage and a singleton daemon before adding replicas]]
[[Dagster worker pools are executor queues, not a pod kind]]

## Related

- [[Scaling Dagster needs Postgres storage and a singleton daemon before adding replicas]]
