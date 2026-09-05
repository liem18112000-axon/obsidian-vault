---
title: "leo-customer360: deploy-sso.sh only restarts Keycloak; the realm/role bootstrap is the separate sso-realm step"
created: 2026-08-22
type: howto
status: seedling
source: "session 2026-08-22"
tags: [leo-customer360, keycloak, sso, deployment, gotcha]
---

# leo-customer360: deploy-sso.sh only restarts Keycloak; the realm/role bootstrap is the separate sso-realm step

In leo-customer360, `deployments/sso/deploy-sso.sh` only (re)runs the **Keycloak container** (docker run + readiness probe). It does NOT provision the realm, roles, client, mappers, or test user — its final line literally says to re-run `../server/deploy-api.sh` next.

The realm provisioning is a **separate step**: `sso-realm` in `deploy-all.sh` (runs `bootstrap-realm.py`, only on the `apply` action — `deploy-all.sh:146`), or run manually `cd deployments/sso && python3 bootstrap-realm.py`.

## Practical consequence
Editing `bootstrap-realm.py` (e.g. adding a role) and then running `./deploy-sso.sh uat` changes **nothing** — the script is never invoked. To apply realm changes: `./deploy-all.sh <env> apply --only sso-realm` (or `--from sso-realm`), or run the Python directly with the right env (`KC_URL`, `REALM`, `CLIENT_ID`, `TENANT_ID`, `TEST_USER`, `REDIRECT_URIS`, plus `KEYCLOAK_ADMIN_PASSWORD` + `KC_TEST_USER_PASSWORD` from `sso/.env`).

Related: [[Creating a Keycloak realm role does not grant it — you must assign it and match the name the app authorizes on]].

## Related

- [[Creating a Keycloak realm role does not grant it — you must assign it and match the name the app authorizes on]]
