---
title: "PG16 NOT NULL check-constraint names are OID-based — exclude when diffing schemas"
created: 2026-08-24
type: gotcha
status: seedling
source: "session 2026-08-24 migration parity test"
tags: [postgres, schema-diff, gotcha, testing]
---

# PG16 NOT NULL check-constraint names are OID-based — exclude when diffing schemas

When diffing two PostgreSQL 16 databases via `information_schema.table_constraints`/`check_constraints`, every `NOT NULL` column shows up as a CHECK constraint whose **name is OID-based** — e.g. `25344_26793_1_not_null` (pattern `<reloid>_<?>_<attnum>_not_null`). Those OIDs differ between any two databases (different object-creation order → different OID allocation), so a naive constraint-name comparison reports **false-positive diffs** even when the schemas are identical.

**Fix when comparing schemas across DBs:** exclude the auto NOT NULL constraints — `WHERE tc.constraint_name NOT LIKE '%\_not\_null' ESCAPE '\'` — and rely on `information_schema.columns.is_nullable` for nullability instead (that column-level fact IS deterministic and comparable). Real named CHECKs (`chk_...`) and PK/FK/UNIQUE names ARE deterministic (derived from table/column names), so keep those.

Hit while building the leo-customer360 migration parity test (dbmate build vs legacy build): every schema/data group matched except `constraints`, whose only diff was the OID-named `*_not_null` rows. Related: [[leo-customer360 uses dbmate for Postgres migrations, not Alembic]].

## Related

- [[leo-customer360 uses dbmate for Postgres migrations]]
- [[not Alembic]]
