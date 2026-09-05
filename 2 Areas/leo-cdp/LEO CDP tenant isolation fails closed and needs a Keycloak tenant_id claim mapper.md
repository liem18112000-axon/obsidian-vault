---
title: "LEO CDP tenant isolation fails closed and needs a Keycloak tenant_id claim mapper"
created: 2026-08-26
type: gotcha
status: seedling
source: "leo-customer360 release-doc work, session 2026-08-26"
tags: [leo-cdp, auth, keycloak, rls, multitenancy, gotcha]
---

# LEO CDP tenant isolation fails closed and needs a Keycloak tenant_id claim mapper

LEO Customer 360's API enforces **fail-closed multi-tenant isolation**: the auth middleware resolves `(tenant_id, user_id)` from the bearer token and pushes them into Postgres session vars `app.tenant_id` / `app.user_id`, which drive Row-Level Security `tenant_policy` policies (FORCE RLS on the `customer360` schema; hardened by `migrations/001_harden_tenant_rls_policies.sql` so a blank `app.tenant_id` matches nothing instead of everything). If no `tenant_id` can be resolved, the request is rejected **401 'Tenant context could not be resolved'**.

Operational gotcha: under `SSO_LOGIN=true`, the Keycloak access token **must carry a `tenant_id` custom claim** (via a protocol mapper on the `leocdp` client). Without it, users authenticate successfully but see **empty data** (and first-login provisioning of `sys_user`/`sys_userinfo` is refused). 'Logged in but no data' almost always means the missing tenant_id mapper.

Auth has two modes via `SSO_LOGIN`: false = local HS256 dev JWT (`DEV_JWT_SECRET`); true = Keycloak OIDC token introspection cached in Redis until token exp. Bearer required on all routes except `/health`, `/api/v1/metadata`, `/api/v1/auth/*`.

## Related

- [[LEO CDP schema migrations are ordered plain SQL]]
- [[not dbmate or alembic]]
