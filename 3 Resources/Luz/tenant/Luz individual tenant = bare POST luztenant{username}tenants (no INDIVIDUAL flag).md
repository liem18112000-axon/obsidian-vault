---
title: "Luz individual tenant = bare POST /luztenant/{username}/tenants (no INDIVIDUAL flag)"
created: 2026-08-17
type: howto
status: seedling
source: "session 2026-08-17 · luz_docs_integration_test investigation"
tags: [luz, tenant, luztenant, luzsec, k6, gotcha]
---

# Luz individual tenant = bare POST /luztenant/{username}/tenants (no INDIVIDUAL flag)

The Luz `luz_docs_integration_test` suite has NO "individual"/"INDIVIDUAL" tenant discriminator anywhere — no `tenantType` field, no separate endpoint. A tenant is created bare and its type is decided **server-side** by luztenant. The company flow simply *upgrades* a bare tenant afterward by attaching a compensation company profile and linking it.

So the recipe for an individual (personal/bare) tenant is: do the tenant bootstrap and **stop before** the two company-only calls.

Steps (base URLs via api-forwarder `localhost:8080`):
1. Register user — `POST {luz_registration}/internal/users` (Basic cron `admin:admin`, form: email/password/firstName/lastName/termsAndConditions/isVerificationEmail). 409 = already exists, treat as success.
   - Seed refresh token — `POST {luzsec}/refreshtokens/initial` (form: username/password/clientId=klara).
2. User JWT — `POST {luzsec}/tokens` (Basic user:pwd) -> `.token`.
3. **Create tenant** — `POST {luztenant}/{username}/tenants?skipMigration=true` (Bearer user JWT, NO body) -> tenant object OR a list (pick max `.id`) -> `.tenantId`. THIS bare call is the individual tenant.
4. Tenant-scoped token — `POST {luzsec}/{tenantId}/tokens` (Basic user:pwd, body `{}`) -> `.token`, used for all later doc work.

Company-only (SKIP for individual): `POST {luz_compensation}/{tenantId}/companies` + `POST {luztenant}/{tenantId}/tenants/company-tenants`.

Gotcha for load tests: reusing one username 409s on register and returns the SAME tenant, so generate a unique email per VU/iteration to actually provision fresh tenants.

Source files in the IT repo: `core/service/tenant_creation_service.py`, `core/service/user_creation_service.py`, `features/environment.py`. Replicated as a k6 script at `luz_docs_import/k6/scripts/luz_create_individual_tenant.js`.

## Related

- [[luz-docs-import zip import flow]]
