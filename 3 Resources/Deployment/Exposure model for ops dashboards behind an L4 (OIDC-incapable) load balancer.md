---
title: "Exposure model for ops dashboards behind an L4 (OIDC-incapable) load balancer"
created: 2026-08-22
type: lesson
status: seedling
source: "leo-customer360 deployments/monitoring, session 2026-08-22"
tags: [deployment, load-balancer, oauth2-proxy, pgadmin, security, leo-customer360]
---

# Exposure model for ops dashboards behind an L4 (OIDC-incapable) load balancer

An L4 Network Load Balancer only forwards TCP — it cannot terminate TLS or speak OIDC — so each ops/monitoring dashboard container behind it must decide *for itself* how login and encryption happen. When adding a new dashboard (Portainer, Netdata, Jaeger, pgAdmin in leo-customer360 deployments/monitoring), pick one of three exposure models based on two properties of the tool: **(1) does it have its own login?** and **(2) does it serve TLS itself?**

- **Direct** — tool has its own login AND serves HTTPS (e.g. Portainer on :9443, self-signed). LB does raw TCP passthrough to the tool's port; the tool authenticates and encrypts. Bonus reason for Portainer specifically: its CSRF/Origin check rejects mutating requests behind a reverse proxy, so it *must not* be gated.
- **SSO-gated** — tool has NO native auth (e.g. Netdata, Jaeger). Put an oauth2-proxy (Keycloak OIDC) in front on its own port; LB TCP-forwards to the proxy, proxy enforces login then forwards to the tool on loopback. This is how you add auth the L4 LB can't.
- **Tunnel-only** — tool is sensitive AND serves only cleartext HTTP with no TLS of its own. Bind it to loopback (127.0.0.1) and reach it over an SSH tunnel; do NOT put it on the LB, because on an all-HTTP L4 path a public port would ship the login in cleartext. This is the most conservative option for a DB/admin tool.

A cleartext-HTTP tool that must be public has **two** valid options: SSO-gate it (oauth2-proxy) or front it with TLS (Caddy). In leo-customer360, pgAdmin (a DB admin tool, own login but plain HTTP) is **SSO-gated** like Netdata — the user chose Keycloak SSO over tunnel-only.

**Key gotcha that drives the choice:** having a native login is NOT enough to expose a tool directly — you also need TLS on the path. Portainer is direct because it *also* serves HTTPS.

**Subtle caveat about SSO-gating a cleartext tool on an all-HTTP LB:** oauth2-proxy adds *access control* (only Keycloak users get through) but NOT *transport encryption*. On uat, which "rides HTTP end-to-end", the Keycloak login, the session cookie, and the tool's own login still traverse plain HTTP — so gating pgAdmin/Netdata there protects *who* can reach it, not *confidentiality in transit*. For real transport security you still need TLS (front it with Caddy at an https sub-path, like Jaeger at https://domain/jaeger) or use the SSH tunnel.

Escape hatch: an OIDC-capable L7 proxy (Caddy, in deployments/proxy) can serve any of these under a TLS sub-path (e.g. Jaeger at https://domain/jaeger), sidestepping the L4 limitation entirely.

Source: leo-customer360 deployments/monitoring deploy-monitoring.sh + README (pgAdmin addition, 2026-08).

See also [[oauth2-proxy gates a dashboard that has no native auth]] and category index [[Deployment]].

## Related

- [[Deployment]]
