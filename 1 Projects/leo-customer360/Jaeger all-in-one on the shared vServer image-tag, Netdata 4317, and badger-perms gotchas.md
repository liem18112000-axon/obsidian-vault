---
title: "Jaeger all-in-one on the shared vServer: image-tag, Netdata :4317, and badger-perms gotchas"
created: 2026-08-21
type: lesson
status: seedling
source: "session 2026-08-21"
tags: [leo-customer360, jaeger, docker, netdata, badger, gotcha, ghcr]
---

# Jaeger all-in-one on the shared vServer: image-tag, Netdata :4317, and badger-perms gotchas

Three gotchas hit deploying **Jaeger all-in-one** as a Docker container on the leo-customer360 monitoring box (co-located with Netdata), plus a stale-image finding.

## 1. Image tag format: 1.x.Y not 1.x
`jaegertracing/all-in-one:1.62` **does not exist** on Docker Hub (`... not found`). The published pins are patch-versioned: **`1.62.0`**, `1.63.0`, … (a bare `1.60`/`1.59` exists for some minors but NOT 1.62). Always pin the full `1.x.y`. (Jaeger v1 all-in-one is `jaegertracing/all-in-one`; v2 is `jaegertracing/jaeger` with a different config model.)

## 2. Netdata's otel-plugin owns host :4317 -> OTLP gRPC conflict
Recent `netdata/netdata:stable` (host network) bundles an **`otel-plugin` that LISTENS on 127.0.0.1:4317** (OTLP gRPC). A co-located Jaeger publishing `-p 0.0.0.0:4317:4317` fails: **`bind host port 0.0.0.0:4317: address already in use`**. Fix: **do NOT publish Jaeger's gRPC 4317 on the host** — publish only OTLP/HTTP `:4318` (which is what the OTel exporters use here anyway). gRPC still works in-container; it's just not host-published.

## 3. Badger storage on a named volume needs a writable dir
Jaeger all-in-one runs as a **non-root uid (10001)**; a fresh Docker **named volume is root-owned**, so `BADGER_DIRECTORY_KEY=/badger/key` fails with **`mkdir /badger/key: permission denied`** and the container crash-loops. Fix: run the container **`--user root`** (or pre-chown the volume to 10001). We chose `--user root` for this internal dev tool.

## 4. Stale image: 'CD deployed' but the box ran an old local build
Symptom: deployed `customer360-api` had no `opentelemetry-instrument` even though CI built+pushed an OTel `:latest`. Cause: the box was running a **locally-built `customer360-api:latest` (image with no registry prefix)** from an old `BUILD_LOCAL` run, and its pulled `ghcr.io/.../customer360-api:latest` copy was stale — CD's `--deploy-uat` never refreshed it. Fix: a clean GHCR redeploy (`./deploy-api.sh uat`, default mode) `docker pull`s the fresh `:latest` and runs it. Tell by `docker inspect --format {{.Config.Image}}`: a bare name = local build; `ghcr.io/...` = the CI image.

## Related
[[Trace FastAPI with OpenTelemetry zero-code instrumentation emitting OTLP]]
[[leo-customer360 tracing: OTel off-by-default on UAT, on at 10% on PROD]]

## Related

- [[Trace FastAPI with OpenTelemetry zero-code instrumentation emitting OTLP]]
- [[leo-customer360 tracing: OTel off-by-default on UAT]]
- [[on at 10% on PROD]]
