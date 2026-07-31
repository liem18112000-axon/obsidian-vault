---
ai_hash: ab40244d0430cede
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-30
entities: []
source: PROD jwt-service investigation 2026-06-30
status: seedling
tags:
- istio
- gcp-logging
- keycloak
- gotcha
- klara
title: Off-mesh services (istio inject=false) have no Istio access logs
type: lesson
---

# Off-mesh services (istio inject=false) have no Istio access logs

A workload running **off the Istio mesh** (`k8s-pod/sidecar_istio_io/inject=false`) produces **no Envoy access logs** — it will not appear in `server-accesslog-stackdriver` as a `destination_workload`, and you cannot use the `DC`/latency/response_flag technique on traffic to it. Example: `luz-keycloak` in klara-prod is off-mesh.

**Consequence:** to assess such a services latency/health you must fall back to its own APP logs (request start/end timing pairs) and to the CALLERs app logs / client timing — the mesh gives you nothing. During the 2026-06-30 incident this is why Keycloaks health had to be judged from jwt-services app-log round-trip timings (72–215ms) rather than from Istio.

Technique base: [[Istio DC response_flag with round latency = caller read timeout]].

## Related

- [[Istio DC response_flag with round latency = caller read timeout]]

%% ai-graph-start %%

**Related notes:**
- [[Istio DC response_flag with round latency = caller read timeout]]
- [[Log red herrings enclosing class name and baseline-noise lines]]
- [[Cascading DC follow the timeout chain one layer down]]
- [[Luz caller read-timeout settings to jwt-service]]
- [[Downstream timeout must sit well below caller timeout (fail-fast ladder)]]

%% ai-graph-end %%