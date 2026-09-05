---
title: "Postgres session SET vs transaction-local set_config for RLS context"
created: 2026-09-05
type: lesson
status: seedling
source: "c360 python code review 2026-09-05"
tags: [postgres, rls, connection-pool, gotcha]
---

# Postgres session SET vs transaction-local set_config for RLS context

In Postgres, `SET app.tenant_id = x` is SESSION-scoped: it persists after COMMIT and, with a connection pool, remains set when the connection is handed to the next borrower. A background worker that sets tenant context this way and then relies on RLS will read/write the *previous* tenant's rows on a reused connection.

Use transaction-local scope instead: `SET LOCAL app.tenant_id = x` or `select set_config('app.tenant_id', v, true)` (third arg true = tx-local), so it resets automatically at transaction end. Per-request handlers should also use tx-local scope.

## Related

- [[Postgres RLS should be defense-in-depth]]
- [[not the sole tenant boundary]]
