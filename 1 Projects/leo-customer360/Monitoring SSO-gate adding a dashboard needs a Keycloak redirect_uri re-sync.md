---
title: "Monitoring SSO-gate: adding a dashboard needs a Keycloak redirect_uri re-sync"
created: 2026-08-21
type: lesson
status: seedling
source: "session 2026-08-21"
tags: [leo-customer360, oauth2-proxy, keycloak, sso, jaeger, gotcha]
---

# Monitoring SSO-gate: adding a dashboard needs a Keycloak redirect_uri re-sync

**leo-customer360** pattern + gotcha for exposing a no-auth web UI (Netdata, now Jaeger) publicly behind SSO.

## The pattern (deployments/monitoring)
The L4 NLB can't do OIDC, so each no-native-auth dashboard is fronted by its OWN **oauth2-proxy** container (a Keycloak confidential client `c360-oauth2-proxy` in the `customer360` realm):
- UI binds **loopback** (127.0.0.1:PORT); only its oauth2-proxy reaches it.
- oauth2-proxy listens on a dedicated backend port (Portainer skips this — it has its own login + a reverse-proxy CSRF check, so it's exposed DIRECT).
- LB backend maps **public port -> box:proxy_port** (health `/ping`).
- Per-dashboard toggle: `<x>_sso=true` + `oauth2_enabled=true` => `<X>_GATED`; each gated dashboard adds its `http://<oauth2_public_host>:<public_port>/oauth2/callback` to the client's redirect URIs.
- To add another gated UI (e.g. Jaeger): mirror the Netdata vars (J_SSO/J_GATED/J_PROXY/J_REDIRECT), call the shared `run_proxy` helper, add a `jaeger` LB backend. Chose proxy port 4686 for Jaeger's 16686 UI.

## The gotcha (bit me)
`deploy-monitoring.sh` **skips the Keycloak client bootstrap entirely when `OAUTH2_PROXY_CLIENT_SECRET` is already in `.env`** (to avoid needing the KC admin password every run). So on an env that already has the client, adding a NEW gated dashboard does NOT register its callback URL -> oauth2 login fails with **"Invalid redirect_uri"**. (The `bootstrap-oauth2-client.py` script itself DOES upsert redirectUris via PUT — it's the deploy wrapper's skip that's the trap.)
Fix: add the URI under the client's *Valid redirect URIs* in Keycloak, OR comment out `OAUTH2_PROXY_CLIENT_SECRET` in `.env` and re-run `./deploy-monitoring.sh <env>` (re-bootstraps, upserts all redirects, rewrites the same secret).

## Related
[[leo-customer360 deploys as Docker containers on VNG vServer VMs over SSH]]
[[leo-customer360 tracing: OTel off-by-default on UAT, on at 10% on PROD]]

## Related

- [[leo-customer360 deploys as Docker containers on VNG vServer VMs over SSH]]
- [[leo-customer360 tracing: OTel off-by-default on UAT]]
- [[on at 10% on PROD]]
