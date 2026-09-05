---
title: "Portainer CSRF origin-invalid behind a reverse proxy - expose it directly"
created: 2026-08-19
type: howto
status: seedling
source: "session 2026-08-19"
tags: [portainer, oauth2-proxy, csrf, reverse-proxy, keycloak, gotcha]
---

# Portainer CSRF "origin invalid" behind a reverse proxy — expose it directly

**Symptom:** in Portainer, a mutating action (e.g. "Unable to create tag") fails with
**"Forbidden - origin invalid"** when Portainer is reached through a reverse proxy
(oauth2-proxy, nginx, an L7 LB).

**Cause:** Portainer enforces a CSRF check that compares the request's `Origin` header host
to the host Portainer itself sees. A proxy hop changes `Host`/`Origin` (proxy talks to the
upstream as `127.0.0.1:9443` while the browser's Origin is the public URL) → mismatch →
Portainer rejects all POST/PUT.

**Fix / decision:** don't put Portainer CE behind an auth proxy. It already has its OWN
login, so expose it **directly** (e.g. LB → Portainer `:9443`, L4 TLS passthrough). Reserve
the SSO gate (oauth2-proxy → Keycloak) for tools that have NO auth of their own, like the
Netdata agent. Gating Portainer would require Portainer **EE** (native OIDC) or a proxy
carefully rewriting `Origin` to match — rarely worth it.

Applied in `leo-customer360` `deployments/monitoring` as a per-dashboard flag
`portainer_sso=false` / `netdata_sso=true`. Related:
[[Gating a dashboard behind Keycloak when the LB is L4 - use oauth2-proxy]]
