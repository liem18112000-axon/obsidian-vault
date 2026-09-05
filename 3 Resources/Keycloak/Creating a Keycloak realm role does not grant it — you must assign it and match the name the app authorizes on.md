---
title: "Creating a Keycloak realm role does not grant it — you must assign it and match the name the app authorizes on"
created: 2026-08-22
type: lesson
status: seedling
source: "session 2026-08-22"
tags: [keycloak, sso, rbac, authorization, gotcha]
---

# Creating a Keycloak realm role does not grant it — you must assign it and match the name the app authorizes on

Provisioning a realm role in Keycloak (`POST /admin/realms/{realm}/roles`) only creates the role **definition**. On its own it grants nobody anything: a login token will not carry the role until it is **assigned** to the user (or a group), and the app must **authorize on the same role name** the token actually contains.

## Why a "created" role can still be a no-op
1. Not assigned -> `realm_access.roles` in the token never includes it (only Keycloak defaults like `default-roles-<realm>`, `offline_access`, `uma_authorization`).
2. Name mismatch -> even if assigned, the app gate must check for that exact name.

## Concrete instance (leo-customer360)
`deployments/sso/bootstrap-realm.py` idempotently creates a `root` realm role but never assigns it to the `c360admin` test user. Meanwhile the API gate `require_admin` (customer360-api/core/auth.py:441) and the frontend `isAdmin()` (config.js:557-559) both authorize on the **`admin`** role, and `segment_api` admin sets are `{platform_admin, super_admin, system_admin, tenant_admin, admin}` — none include `root`. The `root`/`is_root` path in `auth_api.py:174` is the **dev local-JWT** login minting its own `roles:["root"]`, not a Keycloak-sourced role. Net: the `root` realm role is orphaned — harmless but functionally inert for SSO auth.

## Fix pattern
To make an SSO user an admin: create the role the app checks (`admin`), then POST a role-mapping to the user: `POST /admin/realms/{realm}/users/{uid}/role-mappings/realm` with the role representation `[{id,name}]` (idempotent: skip if already mapped).

Related: [[leo-customer360: deploy-sso.sh only restarts Keycloak; the realm/role bootstrap is the separate sso-realm step]], [[leo-customer360 frontend SSO=false because CD deploys the API with SSO_LOGIN=false]].

## Related

- [[leo-customer360: deploy-sso.sh only restarts Keycloak; the realm/role bootstrap is the separate sso-realm step]]
- [[leo-customer360 frontend SSO=false because CD deploys the API with SSO_LOGIN=false]]
