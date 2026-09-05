---
title: "Caddy handle_path strips the path prefix, handle keeps it"
created: 2026-08-19
type: howto
status: seedling
source: "leo-customer360 deployments/proxy, 2026-08"
tags: [caddy, reverse-proxy, routing, fastapi]
---

# Caddy handle_path strips the path prefix, handle keeps it

In a Caddyfile, `handle_path /p/*` **strips** the matched prefix before proxying, while `handle /p/*` forwards the path **as-is**. Choose based on what the upstream expects at its own root.

**How to apply (single public host, path-routed):**
- FastAPI app with `root_path=/c360api` → `handle_path /c360api/*` so the app receives its real `/api/v1/...` routes (root_path still fixes generated/doc URLs).
- Keycloak served under `/auth` (via `KC_HTTP_RELATIVE_PATH=/auth`) → `handle` (NOT handle_path): Keycloak already serves under `/auth`, so forward the prefix.
- The bare `handle { ... }` catch-all (e.g. the frontend) must be the LAST block; specific handle/handle_path blocks match first.

Apps that emit absolute URLs or fixed callback paths (Dagster, oauth2-proxy, Portainer) do NOT sub-path cleanly without app-side prefix config — a subdomain is usually cleaner than a path for those.

Related: [[Keycloak behind a TLS-terminating proxy needs proxy headers and hostname-strict off]]
