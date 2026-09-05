---
title: "leo-customer360 deploys as Docker containers on VNG vServer VMs over SSH"
created: 2026-08-21
type: observation
status: seedling
source: "session 2026-08-21"
tags: [leo-customer360, LEOCDP, VNG-Cloud, infra, deployment]
---

# leo-customer360 deploys as Docker containers on VNG vServer VMs over SSH

The **leo-customer360 / LEOCDP Customer360** platform deploys to **VNG Cloud vServer VMs** (IaaS), **not Kubernetes** — the `k8s/` dir in the repo is a local/alt path only. Each service runs as a **Docker container on the VM**, images are CI-built and pulled from **GHCR**, and deployment happens **over SSH** via per-module bash scripts orchestrated by `deployments/deploy-all.sh`.

## Deploy orchestration
`deploy-all.sh <uat|prod>` runs steps in dependency order: storage, postgres, server, db-schema, cache, sso, backend, load-balancer, proxy, sso-realm, api, frontend, ads, monitoring, seed.

Step -> script map:
- api -> `deployments/server/deploy-api.sh`
- ads -> `deployments/ads-server/deploy-ads.sh`
- frontend -> `deployments/frontend/deploy-frontend.sh`
- monitoring -> `deployments/monitoring/deploy-monitoring.sh`
- backend (Dagster) -> `deployments/server/deploy-backend.sh`

Deploy scripts build the container env locally and ship it **base64-encoded** over SSH, then `docker run` on the box.

## UAT topology (small!)
2 boxes, both `s-general-1x2` = **1 vCPU / 2 GB**:
- **api box** (private 10.100.1.5) co-locates EVERYTHING: customer360-api(8008) + ads-server(9009) + frontend-admin(8890) + Keycloak(8080/9000) + Redis(6580, container) + Portainer(9443) + Netdata(19999).
- **backend box**: Dagster only (this is the "except dagster" split).

## PROD topology
Dedicated vServer per service: api (`s2-general-4x8`), sso (Keycloak), frontend, ads (4x8). Redis becomes managed **MemStore**. Monitoring defaults to the api box but the overlay recommends a dedicated `mon` box (`mon_server_key`) once under load.

## Front door & misc
L4 **NLB -> Caddy** reverse proxy (TLS + path routing) on the box. The LB can't do OIDC, so **oauth2-proxy** (Keycloak confidential client `c360-oauth2-proxy` in the `customer360` realm) gates the dashboards. Public host **beta.leocdp.com**. Only AZ enabled on this account is **HCM03-1C**.

Stack: FastAPI/uvicorn (Python 3.11) + SQLAlchemy 2 + Postgres (pgvector/PostGIS) + Redis + Keycloak + Dagster + Kafka + MinIO.

## Related
[[Trace FastAPI with OpenTelemetry zero-code instrumentation emitting OTLP]]

## Related

- [[Trace FastAPI with OpenTelemetry zero-code instrumentation emitting OTLP]]
