---
title: "Scale one uvicorn service into N replicas on one VM with a docker bridge + local nginx LB"
created: 2026-08-31
type: howto
status: seedling
source: "session 2026-08-31 deploy-tracking.sh"
tags: [docker, nginx, load-balancing, fastapi, uvicorn, deployment, leo-customer360]
---

# Scale one uvicorn service into N replicas on one VM with a docker bridge + local nginx LB

To scale a single-process app server (e.g. uvicorn/FastAPI, one process per container) on a single VM without touching the fronting proxy, run **N replicas on a user-defined docker bridge + one local nginx `least_conn` LB** that publishes the *same* host port the proxy already targets. The proxy (Caddy) and any external NLB see one unchanged upstream (`ip:8010`); all fan-out is contained in the deploy script.

## Why this shape

- **Replicas on a user bridge keep the SAME internal port.** Each container listens on `:8010` in its own network namespace, so there is no per-instance `--port` override and the container's built-in Dockerfile `HEALTHCHECK` (which probes `localhost:8010`) stays valid. No app change.
- **nginx resolves replicas by container name.** nginx joins the same bridge and uses Docker's embedded DNS (`127.0.0.11`) to reach `app-1:8010 … app-N:8010`; it publishes `-p 8010:8010` to the host so the proxy reaches it.
- **Outbound still works without `--network host`.** Bridge containers reach external deps (S3, cross-box Redis, OTLP/Jaeger) via NAT with the *host's* source IP — so cross-box firewall rules keyed on the box IP keep matching. Host networking is unnecessary here.
- **Failover** comes from `least_conn` + `max_fails`/`fail_timeout` + `proxy_next_upstream error timeout http_502 http_503 http_504` — a replica restart just drains to the others.

## Gotcha — static upstreams resolve ONCE

An nginx static `upstream { server name:8010; }` block resolves the names **at config load (startup)**, not per request. So: **start all replicas before nginx**, and rely on `--restart unless-stopped` keeping each container's bridge IP stable across crashes (same container = same IP). A full redeploy recreates nginx too, so it re-resolves. Adding a `resolver` directive does NOT help a static upstream (only helps when the target is a variable in `proxy_pass`).

## Concrete use

`leo-customer360` `deployments/server/deploy-tracking.sh`: `data-tracking-api` runs as `TRACKING_REPLICAS` instances (default uat 3 / prod 5) named `customer360-tracking-api-1..N` on bridge `c360-tracking`, behind `customer360-tracking-lb` (nginx:alpine) on `:8010`. Lowering the count re-runs and removes the surplus. See [[PARA/Areas]] leo-customer360 deploy notes.

## Related

- [[Docker embedded DNS resolves container names on a user-defined bridge]]
- [[nginx least_conn upstream]]
