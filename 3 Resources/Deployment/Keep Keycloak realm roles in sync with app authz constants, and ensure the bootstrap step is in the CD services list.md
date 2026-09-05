---
title: "Keep Keycloak realm roles in sync with app authz constants, and ensure the bootstrap step is in the CD services list"
created: 2026-08-23
type: lesson
status: seedling
source: "leo-customer360 deployments/sso + cd.yml, session 2026-08-23"
tags: [keycloak, rbac, cd, idempotency, leo-customer360, gotcha]
---

# Keep Keycloak realm roles in sync with app authz constants, and ensure the bootstrap step is in the CD services list

Two related traps found wiring Keycloak SSO for customer360-api:

**1. The IdP's roles must match the app's authorization constants.** The API authorizes on hardcoded role sets — `PLATFORM_ADMIN_ROLES = {platform_admin, super_admin, system_admin}` and `TENANT_ADMIN_ROLES = that | {tenant_admin, admin}` (customer360-api/core/routers/segment_api.py) — read from the token's `realm_access.roles`. But the realm bootstrap (bootstrap-realm.py) only created a `root` realm role the API never checks, and assigned NO role to the admin test user. Net effect: no user could ever pass the admin checks. Fix: the realm-provisioning script must create exactly the roles the app checks for, and grant one to a bootstrap admin user. Keycloak includes assigned realm roles in `realm_access.roles` by default (via the built-in 'roles' client scope) — no extra mapper needed.

**2. A provisioning step can silently never run because it is not in the CD 'services' list.** deploy-all.sh has an `sso-realm` step, but CD deployed with `--only api,backend,ads,frontend` (DEFAULT_SVC), which EXCLUDES sso-realm — so the realm bootstrap never ran in CD, only when someone remembered to run it manually. The step also needed `KEYCLOAK_ADMIN_PASSWORD`, which the CD runner didn't have. Lesson: an idempotent bootstrap script is worthless if the pipeline's step-selection filter drops it; audit what the CD 'services'/'only' list actually includes, and confirm every step it runs has the secrets it needs.

**Idempotency techniques used:** GET-before-POST per role; re-POSTing a realm role-mapping is a Keycloak no-op; realm/client/user are create-or-update. So the whole bootstrap is safe to run on every deploy.

Source: leo-customer360 deployments/sso/bootstrap-realm.py + .github/workflows/cd.yml (2026-08).

## Related

- [[Exposure model for ops dashboards behind an L4 (OIDC-incapable) load balancer]]
