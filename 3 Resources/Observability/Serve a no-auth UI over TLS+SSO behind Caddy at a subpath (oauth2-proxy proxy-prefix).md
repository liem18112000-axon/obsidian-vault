---
title: "Serve a no-auth UI over TLS+SSO behind Caddy at a subpath (oauth2-proxy proxy-prefix)"
created: 2026-08-21
type: howto
status: seedling
source: "session 2026-08-21"
tags: [caddy, oauth2-proxy, keycloak, sso, tls, hsts, jaeger, reverse-proxy]
---

# Serve a no-auth UI over TLS+SSO behind Caddy at a subpath (oauth2-proxy proxy-prefix)

How to expose a no-native-auth web UI (Jaeger, Netdata, …) at **https://<domain>/<path>** with a valid cert + Keycloak SSO, using the existing Caddy :443 front — and WHY the raw-LB-port approach fails.

## Why not a raw LB port
Fronting the UI on a dedicated L4-LB port as plain HTTP (e.g. http://<lb>:16686 -> oauth2-proxy) lets the initial 302 work, but Keycloak's **oauth2 callback** is `http://<host>:<port>/oauth2/callback`. If the host (e.g. leocdp.com) is **HSTS-preloaded (includeSubDomains)**, the browser force-upgrades that callback to https on a plain-http port -> TLS handshake fails -> login can't complete. Valid TLS on the callback is mandatory.

## The subpath pattern (three coordinated pieces)
1. **Caddy** (existing :443 site, valid cert): add an **UNSTRIPPED** `handle` (NOT handle_path) so the app sees the prefix — `@x path /jaeger /jaeger/*` -> `reverse_proxy {$JAEGER_UPSTREAM}` (points at the oauth2-proxy). Put it before the catch-all handle.
2. **oauth2-proxy**: `--proxy-prefix=/jaeger/oauth2` (its own endpoints live under the prefix), `--redirect-url=https://<domain>/jaeger/oauth2/callback`, `--cookie-secure=true` (now behind TLS), `--reverse-proxy=true` (trust Caddy's X-Forwarded-Proto). Upstream = the app.
3. **The app**: set its base path so it serves under the prefix — Jaeger v1: `QUERY_BASE_PATH=/jaeger`; Netdata/dagster/Grafana have equivalents. Without this, asset/API paths 404.
Then register the https callback on the Keycloak client and drop the dedicated LB listener.

## Gotchas
- handle_path STRIPS the prefix; oauth2-proxy's proxy-prefix + the app's base-path both EXPECT it -> use `handle` (unstripped).
- Caddy auto-reuses the domain's managed cert for the subpath (same host, port 443) — no new cert.
- /ping stays at the proxy root (health check unaffected by proxy-prefix).

## Related
[[Monitoring SSO-gate: adding a dashboard needs a Keycloak redirect_uri re-sync]]
[[Monitoring-dashboard SSO login user is c360admin, not the Keycloak master admin]]

## Related

- [[Monitoring SSO-gate: adding a dashboard needs a Keycloak redirect_uri re-sync]]
