---
title: "Postgres RLS is silently bypassed by superuser connections"
created: 2026-08-09
type: lesson
status: seedling
source: "session 2026-08-09 leo-customer360 terraform adapt"
tags: [postgres, rls, security, multi-tenant, gotcha]
---

# Postgres RLS is silently bypassed by superuser connections

Postgres Row-Level Security policies are **silently bypassed** when a connection is made as a superuser (or any role with the `BYPASSRLS` attribute), and also by the table owner unless `FORCE ROW LEVEL SECURITY` is set. No error, no warning — queries just return/allow everything, so tenant isolation quietly does not apply.

**Consequence for multi-tenant apps:** the application MUST connect as a dedicated **non-superuser** login role (e.g. `customer360_app`) for RLS to enforce anything. If config still sets `DB_USER=postgres` (as the LEO Customer360 k8s `c360-config` did), the RLS policies are inert even though they exist in the schema — a security footgun that looks fine in tests run as postgres.

Provisioning the app role is necessary but not sufficient: the deployed config must actually *use* it.

Related: [[Managed DB provisioners create the server but not in-database objects]].

## Related

- [[Managed DB provisioners create the server but not in-database objects]]
