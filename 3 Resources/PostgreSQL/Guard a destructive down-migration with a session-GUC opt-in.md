---
title: "Guard a destructive down-migration with a session-GUC opt-in"
created: 2026-08-24
type: howto
status: seedling
source: "session 2026-08-24 dbmate implementation"
tags: [dbmate, migrations, postgres, rollback, safety]
---

# Guard a destructive down-migration with a session-GUC opt-in

A baseline (whole-schema) migration'\''s `-- migrate:down` is usually `DROP SCHEMA x CASCADE` — total data loss. Running `dbmate down` one step too far can wipe a database. Make it **safe-by-default with an explicit opt-in** using a Postgres session GUC guard:

```sql
-- migrate:down
DO $$
BEGIN
  IF current_setting('\''app.allow_destructive_down'\'', true) IS DISTINCT FROM '\''true'\'' THEN
    RAISE EXCEPTION '\''Refusing to DROP SCHEMA x (data loss). Set app.allow_destructive_down=true to confirm.'\'';
  END IF;
END $$;
DROP SCHEMA IF EXISTS x CASCADE;
```

**Why it works:** `current_setting('\''app.allow_destructive_down'\'', true)` (2nd arg = missing_ok) returns NULL for an unset custom GUC instead of erroring; `IS DISTINCT FROM '\''true'\''` is TRUE, so it RAISEs. dbmate wraps each down in a transaction, so the RAISE rolls the whole thing back: schema untouched AND the migration stays marked applied (dbmate only unmarks on success). Verified: refused down left all tables intact and the row in schema_migrations.

**Opt-in teardown** — pass the GUC on the connection via libpq options in the URL:
`DATABASE_URL="...&options=-c%20app.allow_destructive_down%3Dtrue" dbmate down` (`%20`=space, `%3D`=`=`). Then down→up round-trips cleanly.

Applies to any migration tool that runs the down in a transaction (dbmate, and by extension anything using plain psql-in-a-txn). Related: [[leo-customer360 uses dbmate for Postgres migrations, not Alembic]], [[Never pipe a dbmate migration file through a raw psql replay — its down section is destructive]].

## Related

- [[leo-customer360 uses dbmate for Postgres migrations]]
- [[not Alembic]]
- [[Never pipe a dbmate migration file through a raw psql replay — its down section is destructive]]
