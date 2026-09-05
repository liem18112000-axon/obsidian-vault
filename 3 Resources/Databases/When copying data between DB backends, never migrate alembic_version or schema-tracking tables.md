---
title: "When copying data between DB backends, never migrate alembic_version or schema-tracking tables"
created: 2026-09-03
type: lesson
status: seedling
source: "leo-customer360 UAT import dry-run, 2026-09-03"
tags: [database, migration, alembic, dagster, gotcha]
---

# When copying data between DB backends, never migrate alembic_version or schema-tracking tables

When bulk-copying rows between two databases that are each managed by Alembic (or any migration tool) — e.g. moving Dagster storage from SQLite to PostgreSQL — **do not copy the `alembic_version` table** (nor other schema/bookkeeping tables). Both databases were migrated independently to their own schema revision. `alembic_version` is a tiny table holding the current revision id; inserting the source DB's revision into the destination gives Alembic **multiple/ambiguous heads**, and the next `migrate`/upgrade fails.

More generally, a table-name-routing copier should carry an EXCLUDE denylist of tables that represent **schema state or ephemeral/rebuildable runtime state**, not history:
- `alembic_version` — schema revision (the critical one).
- instance identity (`instance_info`), rebuildable indexes (`secondary_indexes`), daemon heartbeats, concurrency slots/limits, pending steps, generic KV stores.
Copy only the real HISTORY tables (in Dagster: runs, event_logs, run_tags, job_ticks, snapshots, instigators, jobs, asset_*).

Method note: a dry-run that only COUNTS source rows (short-circuiting before the INSERT) will still list these dangerous tables — which is exactly how this was caught. Always eyeball the dry-run table list, not just the totals, before committing an import.

## Related
[[Dagster has no supported storage-backend migration; run history is operational metadata]]

## Related

- [[Dagster has no supported storage-backend migration; run history is operational metadata]]
