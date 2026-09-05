---
title: "Keycloak behind a TLS-terminating proxy needs proxy headers and hostname-strict off"
created: 2026-08-19
type: lesson
status: seedling
source: "leo-customer360 deployments, 2026-08"
tags: [keycloak, oidc, reverse-proxy, tls]
---

# Keycloak behind a TLS-terminating proxy needs proxy headers and hostname-strict off

When Caddy (or any L7/TLS-terminating proxy) sits in front of Keycloak, set `KC_PROXY_HEADERS=xforwarded` and `KC_HOSTNAME_STRICT=false` (plus `KC_HTTP_ENABLED=true` since the proxy speaks HTTP to KC) — and this is needed even in `start-dev`, not only production `start`. Otherwise Keycloak trusts the internal request scheme/host and builds `http://` issuer/redirect URLs (or rejects the forwarded host).

Set `KC_HOSTNAME=https://<public-host>`. If the proxy path-routes Keycloak under a sub-path, also set `KC_HTTP_RELATIVE_PATH=/auth` — then the OIDC **issuer** becomes `https://<host>/auth/realms/<realm>`.

**Gotcha:** every consumer must use the IDENTICAL issuer string — API token introspection, oauth2-proxy OIDC discovery, and the browser authorize URL. A mismatch (missing `/auth`, http vs https) fails validation. Changing `KC_HOSTNAME` changes the issuer, so all existing tokens/sessions become invalid (one forced re-login).

Related: [[Caddy handle_path strips the path prefix, handle keeps it]], [[Keycloak 24+ token introspection requires the client in the token audience]]

## Related

- [[Keycloak 24+ token introspection requires the client in the token audience]]
