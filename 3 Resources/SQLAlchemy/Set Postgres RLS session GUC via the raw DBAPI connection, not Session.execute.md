---
title: "Set Postgres RLS session GUC via the raw DBAPI connection, not Session.execute"
created: 2026-08-20
type: lesson
status: seedling
source: "session 2026-08-20, leo-customer360 commit 4da1868"
tags: [sqlalchemy, postgres, rls, multi-tenant, customer360-api, gotcha]
---

# Set Postgres RLS session GUC via the raw DBAPI connection, not Session.execute

When application code must set a Postgres Row-Level-Security session GUC (e.g. `SELECT set_config('app.tenant_id', :t, true)` for multi-tenant RLS) as a side effect during a request, route that statement through the **raw DBAPI connection** — `db.connection().execute(text(...))` — not through `Session.execute(...)`.

**Why it matters (design decision):** In `customer360-api`, login provisioning (`core/auth._get_or_create_user_on_login`) needs the `app.tenant_id` GUC pinned so RLS applies. Doing it via `Session.execute` puts an extra statement onto the ORM session in the exact place the repository issues its SELECT/INSERT/UPDATE — which broke the unit tests that script a fixed sequence of session results (see [[A scripted-sequence test double breaks when production code adds a Session.execute call]]).

**The pattern:** wrap it in a helper guarded so unit doubles no-op:
```python
def _pin_transaction_tenant(db, tenant_id: str) -> None:
    if getattr(db, "connection", None):
        db.connection().execute(
            text("SELECT set_config('app.tenant_id', :t, true)"), {"t": tenant_id})
```
Real SQLAlchemy sessions have a live `.connection()`, so RLS is enforced in prod; a `FakeDBSession` test double has none, so the call is a no-op and the session sees only the repository statements. The `true` third arg to `set_config` makes it transaction-local.

Fixed in commit `4da1868` on `leo-customer360` (branch `infras/continue-integration/v1`); regression was introduced by `7ded3b5`.

## Related

- [[A scripted-sequence test double breaks when production code adds a Session.execute call]]
