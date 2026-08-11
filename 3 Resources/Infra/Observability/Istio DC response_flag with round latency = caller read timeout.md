---
ai_hash: bbf814035b6b0f24
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-30
entities: []
source: PROD jwt-service investigation 2026-06-30
status: seedling
tags:
- istio
- envoy
- gcp-logging
- timeout
- diagnostics
- klara
title: Istio DC response_flag with round latency = caller read timeout
type: lesson
---

# Istio DC response_flag with round latency = caller read timeout

In an Istio/Envoy mesh, per-request **access logs** are separate from the app stdout/stderr for the same `container_name`. On klara-prod they live in `logName="projects/klara-prod/logs/server-accesslog-stackdriver"`, with fields `httpRequest.{status,latency,requestUrl,requestMethod}` and `labels.{response_flag,source_workload,destination_workload,destination_name(=pod),request_id}`.

**Diagnostic pattern — caller-side read timeout:** a `labels.response_flag="DC"` (Downstream Connection termination) row, with `httpRequest.status` **empty** and `httpRequest.latency` clustered at a **round value** (e.g. `30.03s`, `70.07s`), means the **caller** hit its configured read timeout and dropped the connection before the server replied. The latency value ≈ the caller’s configured read timeout. Caller-side this surfaces as `java.net.SocketTimeoutException: Read timed out`. So the round latency directly reveals each caller’s timeout setting, and `DC` proves the server was simply too slow (not erroring).

Filter for these aborted requests with `labels.response_flag!="-"` (normal completed requests log `-`).

**Gotchas:**
- After the caller gives up, the *server*’s own access log may still later show a `200/201` with high latency — it kept processing after the caller bailed. So correlate by `request_id` + time window, not by assuming a failed status on the server side.
- The caller’s `"Read timed out"` log line usually does **not** contain the target URL (the URL is on a different line), so an `AND "jwt"`-style single-line text filter will miss them — search the stack-trace text separately from the target.

Companion: [[3 Resources/Work-Kepler/Klara/klara-prod is a separate GCP project, not a namespace]]. Applied to [[jwt-service token endpoints and replicas (Luz prod)]].

## Related

- [[klara-prod PROD logging access gcloud logging read only]]
- [[no kubectl]]
- [[jwt-service token endpoints and replicas (Luz prod)]]

%% ai-graph-start %%

**Related notes:**
- [[Cascading DC follow the timeout chain one layer down]]
- [[Off-mesh services (istio inject=false) have no Istio access logs]]
- [[Downstream timeout must sit well below caller timeout (fail-fast ladder)]]
- [[Luz caller read-timeout settings to jwt-service]]
- [[Log red herrings enclosing class name and baseline-noise lines]]

%% ai-graph-end %%