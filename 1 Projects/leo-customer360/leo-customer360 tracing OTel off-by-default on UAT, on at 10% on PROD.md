---
title: "leo-customer360 tracing: OTel off-by-default on UAT, on at 10% on PROD"
created: 2026-08-21
type: argument
status: seedling
source: "session 2026-08-21"
tags: [leo-customer360, tracing, opentelemetry, jaeger, design-decision]
---

# leo-customer360 tracing: OTel off-by-default on UAT, on at 10% on PROD

Design decision for API request tracing in **leo-customer360**: instrument all three FastAPI services with OpenTelemetry, but gate it by environment because of the box sizing.

## Decision
- **UAT: tracing OFF by default** (`OTEL_SDK_DISABLED=true`). The UAT `api` vServer is a tiny **1 vCPU / 2 GB** box already shared by api + ads + frontend + Keycloak + Redis + Portainer + Netdata. Always-on instrumentation + a running Jaeger would risk starving it. Instead we profile **on demand**: start the Jaeger container, flip the target service's `OTEL_SDK_DISABLED=false`, capture, then revert. Zero standing overhead.
- **PROD: tracing ON at 10% head sampling** (`parentbased_traceidratio`, arg 0.1). Dedicated per-service 4x8 boxes have headroom.

## Why / How
- **Why:** protect the crowded UAT box; pay for tracing only when actually debugging. PROD can afford continuous low-rate sampling.
- **How it's wired:** a shared `deployments/lib/otel.sh` (`otel_env_lines <svc> <env> [jaeger_host]`) emits the OTEL_* env block, dot-sourced by deploy-api.sh / deploy-ads.sh / deploy-frontend.sh and appended to each service's container env-file. Override per run with `OTEL_ENABLED` / `OTEL_ENDPOINT` / `OTEL_SAMPLER_ARG`.
- **Backend placement:** Jaeger all-in-one (v1, badger on-disk storage, `--memory` capped, `COLLECTOR_OTLP_ENABLED=true`) is added to the existing `deployments/monitoring` module — same deploy script + SSO-gate pattern as Portainer/Netdata. Toggled by `jaeger_enabled` in the monitoring overlays (false on uat, true on prod).
- **Transport:** OTLP/HTTP on 4318 (grpc 4317). UAT services use `--network host` so they hit `127.0.0.1:4318`; PROD per-service boxes reach the monitoring box's Jaeger over the private VPC (deploy scripts resolve `mon_server_key` private IP). Jaeger UI (16686) is loopback-bound -> SSH tunnel. Runbook: deployments/monitoring/README.md (Jaeger section).

## Related
[[leo-customer360 deploys as Docker containers on VNG vServer VMs over SSH]]
[[Trace FastAPI with OpenTelemetry zero-code instrumentation emitting OTLP]]

## Related

- [[leo-customer360 deploys as Docker containers on VNG vServer VMs over SSH]]
- [[Trace FastAPI with OpenTelemetry zero-code instrumentation emitting OTLP]]
