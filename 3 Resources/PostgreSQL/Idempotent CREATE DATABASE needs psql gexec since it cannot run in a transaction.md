---
title: "Idempotent CREATE DATABASE needs psql gexec since it cannot run in a transaction"
created: 2026-08-09
type: howto
status: seedling
source: "session 2026-08-09 leo-customer360 terraform adapt"
tags: [postgres, psql, idempotency, gexec, gotcha]
---

# Idempotent CREATE DATABASE needs psql gexec since it cannot run in a transaction

You cannot run `CREATE DATABASE` inside a transaction block or a `DO $$ ... $$` PL/pgSQL block — Postgres rejects it ("CREATE DATABASE cannot run inside a transaction block"). So the usual `IF NOT EXISTS ... CREATE` idempotency pattern (which lives in a DO block) does not work for databases.

**The idempotent pattern** uses psql's `\gexec` meta-command, which takes each row of a query result and runs it as a SQL command:

```sql
SELECT CREATE DATABASE db_keycloak
WHERE NOT EXISTS (SELECT FROM pg_database WHERE datname = db_keycloak)\gexec
```

If the DB exists the guard SELECT returns zero rows → `\gexec` runs nothing → no-op. If it does not exist, it returns one row → the `CREATE DATABASE` string executes, outside any transaction. Safe to re-run, works with `psql -f` under `ON_ERROR_STOP=1`.

Note: `\gexec` is a psql client feature, not server SQL — it only works when the file is fed through `psql`, not via a generic SQL driver.

Related: [[Managed DB provisioners create the server but not in-database objects]].

## Related

- [[Managed DB provisioners create the server but not in-database objects]]
