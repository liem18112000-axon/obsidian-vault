---
title: "Postgres RLS should be defense-in-depth, not the sole tenant boundary"
created: 2026-09-05
type: lesson
status: seedling
source: "c360 python code review 2026-09-05"
tags: [postgres, rls, multi-tenancy, security, gotcha]
---

# Postgres RLS should be defense-in-depth, not the sole tenant boundary

Relying on PostgreSQL Row-Level Security (RLS keyed on `current_setting('app.tenant_id')`) as the *only* tenant boundary is fragile, because common layers sit outside or above it and silently defeat it:

- a response/read cache in front of the DB returns cached rows without RLS ever running;
- a table created without a `tenant_id` column (and no policy) is unprotected;
- setting `app.tenant_id` from caller-supplied input aims RLS at the attacker's chosen tenant;
- connecting as a superuser or the table-owner role bypasses RLS unless `FORCE ROW LEVEL SECURITY` is set (and even FORCE does not stop `BYPASSRLS`/superusers).

Lesson: treat RLS as defense-in-depth. Also enforce tenant scoping explicitly at the application layer (query filters, cache keys, authorization checks), and run the app as a non-superuser role.

## Related

- [[Postgres session SET vs transaction-local set_config for RLS context]]
- [[Response caches must include the authenticated tenant in the key]]
- [[Derive tenant identity from the verified token]]
- [[never from request input]]
