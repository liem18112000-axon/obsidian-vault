---
title: "Dagster has no supported storage-backend migration; run history is operational metadata"
created: 2026-09-03
type: lesson
status: seedling
source: "leo-customer360 Phase 0 cutover, 2026-09-03"
tags: [dagster, postgres, sqlite, migration, gotcha]
---

# Dagster has no supported storage-backend migration; run history is operational metadata

Dagster has **no supported command to move data between storage backends** (SQLite ⇄ PostgreSQL). `dagster instance migrate` only migrates the *schema* across Dagster versions — it does **not** copy runs/events/cursors from one backend to another. Switching storage therefore starts the new backend **empty**.

Two facts that make this low-risk in practice:
1. **Dagster storage holds operational metadata only** — run/event history + sensor/schedule cursors. The actual business data (app tables, object-store outputs) lives elsewhere. Losing run history is an audit-trail loss, not a data-loss.
2. If a container ran `dagster dev` with an **ephemeral DAGSTER_HOME** (no volume mount), that SQLite history is destroyed on every `docker rm` anyway — so `docker cp <container>:/dagster_home -` it OUT *before* redeploying, or it is gone.

If you must preserve it, a **best-effort import** works: walk every `*.db` under the old DAGSTER_HOME (run storage, schedule storage, and the per-run event-log shards under `history/runs/`), and for each SQLite table copy the rows into the same-named Postgres table over the **intersecting columns**, dropping the autoincrement `id` (Dagster relates rows by the string `run_id`, so Postgres can re-assign ids) and using `INSERT ... ON CONFLICT DO NOTHING` for idempotency. This is version-sensitive — dry-run and compare counts, rehearse on a staging copy.

Consequence of a fresh (un-imported) schedule storage: **poll-sensor cursors reset**, so each sensor re-baselines on its next tick (it does not replay all history). Expect one catch-up evaluation; watch for duplicate runs.

## Related
[[Dagster auto-creates its tables but not the database]]
[[Scaling Dagster needs Postgres storage and a singleton daemon before adding replicas]]

## Related

- [[Dagster auto-creates its tables but not the database]]
