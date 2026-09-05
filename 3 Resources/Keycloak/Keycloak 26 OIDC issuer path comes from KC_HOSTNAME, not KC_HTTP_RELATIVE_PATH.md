---
title: "Keycloak 26 OIDC issuer path comes from KC_HOSTNAME, not KC_HTTP_RELATIVE_PATH"
created: 2026-08-19
type: gotcha
status: seedling
source: "leo-customer360 beta.leocdp.com cutover, 2026-08"
tags: [keycloak, oidc, issuer, hostname, relative-path, gotcha]
---

# Keycloak 26 OIDC issuer path comes from KC_HOSTNAME, not KC_HTTP_RELATIVE_PATH

In Keycloak 26 (hostname v2), `KC_HTTP_RELATIVE_PATH=/auth` makes the server SERVE under `/auth` (and moves the mgmt/health endpoints), but the OIDC **issuer** and other frontend URLs are computed from **KC_HOSTNAME**. If KC_HOSTNAME is a bare origin (`https://host`, no path), the discovery doc reports `issuer: https://host/realms/<realm>` — WITHOUT `/auth` — even though the server only answers under `/auth`. That mismatch breaks every relying party (they discover a URL that 404s).

**Fix:** put the path in the hostname: `KC_HOSTNAME=https://host/auth` (keep `KC_HTTP_RELATIVE_PATH=/auth` when the proxy forwards the prefix un-stripped). Then issuer = `https://host/auth/realms/<realm>` and serving matches. Verified there is NO `/auth/auth` doubling — the hostname path and the relative path both denote the same public mount.

Symptom that first flags it: `.well-known/openid-configuration` returns 200 but its `issuer` field lacks the path prefix.

Related: [[KC_HTTP_RELATIVE_PATH moves Keycloak health endpoints under the prefix too]], [[Redeploy OIDC consumers only after the issuer URL is reachable]]

## Related

- [[KC_HTTP_RELATIVE_PATH moves Keycloak health endpoints under the prefix too]]
