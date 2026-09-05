---
title: "Monitoring-dashboard SSO login user is c360admin, not the Keycloak master admin"
created: 2026-08-21
type: reference
status: seedling
source: "session 2026-08-21"
tags: [leo-customer360, keycloak, sso, login, oauth2-proxy]
---

# Monitoring-dashboard SSO login user is c360admin, not the Keycloak master admin

To log into the SSO-gated leo-customer360 dashboards (Netdata, Jaeger — via oauth2-proxy -> Keycloak), use the **`customer360` realm** user, NOT the Keycloak console admin.

- **Username: `c360admin`** — the ONLY user in the `customer360` realm (created by `deployments/sso/bootstrap-realm.py` as `TEST_USER`, default `c360admin`, email c360admin@example.com, enabled + emailVerified).
- **Password:** the value of **`KC_TEST_USER_PASSWORD`** in `deployments/sso/.env` (bootstrap sets it as a non-temporary password via the KC admin API reset-password call).
- The **`admin` / `KEYCLOAK_ADMIN_PASSWORD`** account is the Keycloak **master-realm console admin** — it is NOT a user in the `customer360` realm, so oauth2-proxy rejects it at the credential step (that's the usual 'cannot login' cause).

Realm login settings: loginWithEmailAllowed=true, registrationAllowed=false, resetPasswordAllowed=false, verifyEmail=false.

Separate gotcha to watch AFTER creds: the oauth2 callback is `http://<oauth2_public_host>:<port>/oauth2/callback` on `beta.leocdp.com`, which is HSTS-preloaded -> browser upgrades to https on a plain-HTTP port and the callback can fail. Affects Netdata and Jaeger identically.

## Related
[[Monitoring SSO-gate: adding a dashboard needs a Keycloak redirect_uri re-sync]]

## Related

- [[Monitoring SSO-gate: adding a dashboard needs a Keycloak redirect_uri re-sync]]
