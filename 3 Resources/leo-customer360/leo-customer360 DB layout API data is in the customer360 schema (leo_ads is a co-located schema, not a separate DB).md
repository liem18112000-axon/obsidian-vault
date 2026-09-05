---
title: "leo-customer360 DB layout: API data is in the customer360 schema (leo_ads is a co-located schema, not a separate DB)"
created: 2026-08-23
type: reference
status: seedling
source: "leo-customer360 uat vDB (10.100.1.3), session 2026-08-23"
tags: [postgres, schema, customer360, leo-ads, keycloak, reference]
---

# leo-customer360 DB layout: API data is in the customer360 schema (leo_ads is a co-located schema, not a separate DB)

The customer360 API connects to the **`customer360` database** (DB_NAME from deployments/postgres overlay). Instance layout (uat vDB 10.100.1.3, verified via pg_class/pg_stat_user_tables):

**Databases:** customer360, db_keycloak, os_admin, postgres. (os_admin + postgres are VNG managed-vDB system DBs; db_keycloak is Keycloak's own DB.)

**Schemas inside the `customer360` DB:**
- **customer360** — the API's data. ~76 tables, ~54k rows: cdp_* (personas/events/profiles/content), crm_*, sys_*. Tenant tables have FORCE ROW LEVEL SECURITY (tenant_id = current_setting('app.tenant_id')).
- **leo_ads** — the ad-server's schema (24 tables), a SEPARATE app CO-LOCATED in the same DB, no RLS. NOTE: despite the deployment docs listing it like a database ("customer360 + db_keycloak + leo_ads"), leo_ads is a SCHEMA, not a database.
- **public** — holds only PostGIS `spatial_ref_sys` (~8500 SRID rows). Reference data, NOT application data.

**Bottom line:** effectively all customer360-API application data is in the `customer360` schema of the `customer360` database. Keycloak data is in the separate db_keycloak database.

**How to check (DB is private — run psql from a VPC box):** connect app_admin@10.100.1.3/customer360 and query pg_class grouped by pg_namespace for table counts + reltuples, and pg_stat_user_tables for n_live_tup. Catalog stats are NOT filtered by RLS, so you can see schema/table layout + row estimates even as the RLS-bound owner without setting app.tenant_id.
