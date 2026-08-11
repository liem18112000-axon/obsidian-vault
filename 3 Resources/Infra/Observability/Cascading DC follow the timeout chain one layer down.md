---
ai_hash: 4863a8fe90c82d5a
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-30
entities: []
source: PROD jwt-service investigation 2026-06-30
status: seedling
tags:
- istio
- timeout
- diagnostics
- root-cause
- klara
title: 'Cascading DC: follow the timeout chain one layer down'
type: lesson
---

# Cascading DC: follow the timeout chain one layer down

When a caller→service call shows an Istio `response_flag=DC` at exactly the callers read timeout, do NOT stop there — **follow the DC chain one layer down**. The service you are blaming may itself be issuing a `DC` against ITS own downstream at the services own (shorter) client timeout. The true stall is at the deepest DC layer; the upper layers are just propagating it.

**Worked example (klara-prod 2026-06-30):** callers saw DC at 24/30/70s inbound to `jwt-service`. But `jwt-service` was itself DC-ing against `luztenant-service` `/security-classes` at its own 30s client timeout (1000+ rows). The real degraded dependency was luztenants Mongo-backed security-class lookup — jwt was an innocent middle hop. The first-pass root cause (a Keycloak hang) was wrong; chasing the DC chain found luztenant.

**How:** query `labels.source_workload="<the-suspect>" AND labels.destination_workload!="..." AND labels.response_flag="DC"` to see what the suspect itself is timing out on. Cross-check with the suspects APP logs for the client-timeout exception (e.g. Vert.x `NoStackTraceTimeoutException: timeout period of 30000ms exceeded`).

Technique base: [[Istio DC response_flag with round latency = caller read timeout]]. Related anti-pattern: [[Downstream timeout must sit well below caller timeout (fail-fast ladder)]].

## Related

- [[Istio DC response_flag with round latency = caller read timeout]]
- [[Downstream timeout must sit well below caller timeout (fail-fast ladder)]]

%% ai-graph-start %%

**Related notes:**
- [[Downstream timeout must sit well below caller timeout (fail-fast ladder)]]
- [[Istio DC response_flag with round latency = caller read timeout]]
- [[Log red herrings enclosing class name and baseline-noise lines]]
- [[Off-mesh services (istio inject=false) have no Istio access logs]]
- [[Luz caller read-timeout settings to jwt-service]]

%% ai-graph-end %%