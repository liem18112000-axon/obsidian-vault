---
title: "leo-customer360 frontend SSO=false because CD deploys the API with SSO_LOGIN=false"
created: 2026-08-22
type: lesson
status: seedling
source: "session 2026-08-22"
tags: [leo-customer360, sso, keycloak, ci-cd, github-actions, gotcha]
---

# leo-customer360 frontend SSO=false because CD deploys the API with SSO_LOGIN=false

The frontend-admin login panel is decided by the **API**, not the frontend container. Even with `sso_login = true` in `deployments/frontend/overlays/uat.tfvars` (frontend deployed `SSO_LOGIN=true`), the UI kept showing the dev credential login because the API published `sso_login: false`.

## The chain
1. Browser: `auth-view.js:139` picks the panel from `metadata.sso_login`, fetched via `config.loadSystemMetadata()` from `<apiRoot>/api/v1/metadata` (`config.js:412`) — the frontend`s own `SSO_LOGIN` env is a red herring.
2. API `/metadata` publishes `settings.sso_login` (`metadata_repository.py:145`), sourced from the API container`s `SSO_LOGIN` env (`config.py:196`).
3. `deployments/server/deploy-api.sh` (~L77-88) sets `SSO_LOGIN=true` only when `SSO_URL`, `KC_REALM`, `KC_CLIENT`, **and** `KC_SECRET` are all non-empty. `KC_SECRET` comes from `deployments/sso/.env` (`KEYCLOAK_CLIENT_SECRET`).
4. `deployments/sso/.env` is git-ignored (written locally by `bootstrap-realm.py`), so it is **absent on the GitHub Actions runner**.
5. In CD, `KC_SECRET` is empty -> config deemed incomplete -> API deploys `SSO_LOGIN=false`. Log tell: `SSO: api_sso_enabled=true but config/secret incomplete — deploying with SSO_LOGIN=false.`

## Net effect
SSO works when the API is deployed from a laptop (where `sso/.env` exists) but silently reverts to false on every CD run.

## Fix
Added a `cd.yml` step that recreates `deployments/sso/.env` from a `KEYCLOAK_CLIENT_SECRET` GitHub secret before `deploy-all.sh` (mirrors `DB_PASSWORD`/`REDIS_PASSWORD` injection). Set the GH secret repo-level (the `uat` environment has no env-scoped secrets; existing secrets are repo-level).

See also [[A git-ignored secret file that a deploy script silently degrades on is a CI foot-gun]].

## Related

- [[A git-ignored secret file that a deploy script silently degrades on is a CI foot-gun]]
