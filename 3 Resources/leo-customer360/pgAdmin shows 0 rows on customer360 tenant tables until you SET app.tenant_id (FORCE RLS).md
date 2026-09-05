---
title: "pgAdmin shows 0 rows on customer360 tenant tables until you SET app.tenant_id (FORCE RLS)"
created: 2026-08-23
type: gotcha
status: seedling
source: "leo-customer360 uat DB, session 2026-08-23"
tags: [postgres, rls, pgadmin, multitenant, customer360, gotcha]
---

# pgAdmin shows 0 rows on customer360 tenant tables until you SET app.tenant_id (FORCE RLS)

**Symptom:** `SELECT * FROM customer360.cdp_master_profiles` in pgAdmin returns NO rows, even though the table has ~700 rows.

**Cause:** tenant tables in the `customer360` schema have **FORCE ROW LEVEL SECURITY** with policy `tenant_id = current_setting('app.tenant_id', true)::uuid`. The DB user `app_admin` is NOT a superuser and does NOT have BYPASSRLS (verified: is_superuser=off, rolbypassrls=f), so the policy applies to it too (FORCE = applies even to the table owner). With no `app.tenant_id` set, `current_setting(...)` is empty -> `tenant_id = NULL` -> zero rows.

**Fix (pgAdmin Query Tool, same session):**
```
SET app.tenant_id = '11111111-1111-1111-1111-111111111111';   -- the seeded/demo tenant on uat
SELECT * FROM customer360.cdp_master_profiles ORDER BY master_profile_id LIMIT 100;
```
Run BOTH together. Caveats: pgAdmin's tree "View/Edit Data" opens a DIFFERENT connection without the SET (still 0 rows) — use the Query Tool. The SET lasts only for that connection.

**Which tables are affected:** the tenant-scoped ones (cdp_master_profiles, sys_user, sys_userinfo, crm_*, sys_data_source, ...). Config/reference/derived tables have NO RLS (cdp_persona_features, cdp_event_catalog, cdp_raw_events_*, cdp_scoring_models, ...) and query fine without a tenant — which is why SOME tables show data and others look empty.

**Discovering tenant ids:** you can't `SELECT DISTINCT tenant_id` from an RLS table without already setting a tenant (RLS filters that too). On uat the seeded tenant is `11111111-1111-1111-1111-111111111111` (matches frontend_tenant_id / bootstrap-realm TENANT_ID). For a cross-tenant DBA view you'd need a superuser or a dedicated BYPASSRLS role (none exists by design — tenant isolation).

**Catalog stats ignore RLS:** pg_class.reltuples / pg_stat_user_tables.n_live_tup show real row counts regardless of RLS, so use those to confirm data EXISTS even when SELECT returns 0.
