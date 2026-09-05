---
title: "Gating a dashboard behind Keycloak when the LB is L4 - use oauth2-proxy"
created: 2026-08-19
type: howto
status: seedling
source: "session 2026-08-19"
tags: [oauth2-proxy, keycloak, oidc, load-balancer, portainer, netdata, security, sso]
---

# Gating a dashboard behind Keycloak when the LB is L4: use oauth2-proxy

**Problem/gotcha:** "Apply Keycloak auth on the load balancer" is impossible on an **L4
(TCP) network LB** — it forwards raw TCP with no HTTP awareness, so it can't run an OIDC
redirect flow. And many admin dashboards can't authenticate themselves:
- **Portainer CE** — OAuth/OIDC is a **Business/EE-only** feature (CE = local logins only).
- **Netdata agent** — SSO is **Netdata Cloud-only**; the self-hosted `:19999` dashboard
  has no login at all.

**Fix:** put **oauth2-proxy** in the path as an OIDC gateway, registered as a confidential
client in the Keycloak realm. The L4 LB forwards the public port → oauth2-proxy (on the
box) → Keycloak login → reverse-proxy to the localhost dashboard:

```
browser → LB :PUB (TCP) → box :PROXY oauth2-proxy → [Keycloak] → 127.0.0.1:DASH
```

Key wiring details learned:
- Point the LB backend at the **proxy port**, not the dashboard port; keep the dashboard
  **loopback/firewalled** (e.g. Portainer `-p 127.0.0.1:9443:9443`).
- `--oidc-issuer-url` must equal the token `iss` exactly (Keycloak = `KC_HOSTNAME/realms/<realm>`);
  the backend box can hairpin to the LB to fetch `.well-known/openid-configuration`.
- L4 preserves the original Host:port, so oauth2-proxy `--redirect-url` = the PUBLIC LB
  URL + `/oauth2/callback`; register both in the KC client's redirectUris.
- Two dashboards on the same host but different ports → give each proxy a distinct
  `--cookie-name` (cookies are per-host, not per-port, so they'd otherwise collide).
- HTTP-only through the LB (no TLS termination) → `--cookie-secure=false`; flip for TLS.
- oauth2-proxy `/ping` is an unauthenticated 200 — use it as the LB health check path.
- Portainer behind SSO is still risky: it holds the **Docker socket = root on the box**;
  one session/auth bypass = host compromise. Prefer keeping it tunnel-only.

Related: [[Portainer vs Netdata - both are web UIs, ops vs metrics]] ·
[[Customer360 UAT api box is a shared 1vCPU-2GB vServer running 5 containers]]
