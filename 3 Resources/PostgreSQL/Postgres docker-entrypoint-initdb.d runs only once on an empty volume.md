---
title: "Postgres docker-entrypoint-initdb.d runs only once on an empty volume"
created: 2026-08-24
type: lesson
status: seedling
source: "session 2026-08-24 migration tooling research"
tags: [postgres, docker, gotcha, migrations]
---

# Postgres docker-entrypoint-initdb.d runs only once on an empty volume

Scripts placed in a Postgres container's `/docker-entrypoint-initdb.d/` (`.sql`/`.sh`) run **exactly once — only when the data directory is empty**, i.e. on the first-ever container start against a fresh volume. They are **never** re-run on subsequent starts once the volume has data.

**The gotcha:** using this as your only schema bootstrap means any later change to `schema.sql` is **silently ignored on every existing database** (dev, UAT, prod all already booted). The change works on fresh volumes and 'mysteriously' does nothing on existing ones — no error, no warning. Symptom: teammates start hand-writing idempotent `DO $$` guard blocks to patch live DBs, which is re-inventing a migration runner by hand.

**Fix:** don't use `initdb` for evolving schema. Keep only truly-once bootstrap there (e.g. `CREATE EXTENSION`), and drive all schema changes through a real migration tool that tracks applied versions (e.g. [[leo-customer360 uses dbmate for Postgres migrations, not Alembic|dbmate]]).

Ref: Docker Hub `postgres` → 'Initialization scripts'.

## Related

- [[leo-customer360 uses dbmate for Postgres migrations]]
- [[not Alembic]]
