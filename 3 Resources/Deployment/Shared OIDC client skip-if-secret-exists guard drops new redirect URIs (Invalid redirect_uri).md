---
title: "Shared OIDC client skip-if-secret-exists guard drops new redirect URIs (Invalid redirect_uri)"
created: 2026-08-22
type: gotcha
status: seedling
source: "leo-customer360 deployments/monitoring, session 2026-08-22"
tags: [keycloak, oauth2-proxy, oidc, gotcha, leo-customer360]
---

# Shared OIDC client skip-if-secret-exists guard drops new redirect URIs (Invalid redirect_uri)

When you add a NEW oauth2-proxy-gated dashboard (e.g. pgAdmin) to a leo-customer360 environment that ALREADY has other gated dashboards, the new dashboard's Keycloak login fails with **"Invalid redirect_uri"** even though the deploy succeeded.

**Root cause:** deploy-monitoring.sh SKIPS the Keycloak client bootstrap (bootstrap-oauth2-client.py) whenever OAUTH2_PROXY_CLIENT_SECRET is already present in .env. All gated dashboards share ONE confidential client (c360-oauth2-proxy), and each needs its own callback URI (e.g. http://<host>:5050/oauth2/callback) in the client's **Valid redirect URIs**. Because the bootstrap is skipped, the new dashboard's callback never gets registered.

**Fix (either):**
1. Manually add the new callback URI under the client's *Valid redirect URIs* in the Keycloak admin console, OR
2. Comment out OAUTH2_PROXY_CLIENT_SECRET in .env and re-run ./deploy-monitoring.sh <env> — the bootstrap then upserts ALL current redirect URIs and rewrites the same secret.

**General lesson:** any 'provision once, skip if secret exists' idempotency guard becomes a trap when the provisioned resource is a SHARED object (one OIDC client for N dashboards) that must be *amended* — not just created — each time you add a consumer. The skip-if-exists shortcut silently drops the amend.

Source: leo-customer360 deployments/monitoring (adding pgAdmin behind Keycloak SSO, 2026-08). Same trap the README documents for Jaeger.

See also [[Exposure model for ops dashboards behind an L4 (OIDC-incapable) load balancer]].

## Related

- [[Exposure model for ops dashboards behind an L4 (OIDC-incapable) load balancer]]
