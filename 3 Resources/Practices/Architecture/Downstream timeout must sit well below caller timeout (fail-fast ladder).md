---
ai_hash: 8e5ad157fa921cd2
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-30
entities: []
source: PROD jwt-service investigation 2026-06-30
status: seedling
tags:
- timeout
- resilience
- circuit-breaker
- microservices
- klara
title: Downstream timeout must sit well below caller timeout (fail-fast ladder)
type: lesson
---

# Downstream timeout must sit well below caller timeout (fail-fast ladder)

A services client-side timeout on a downstream call must sit **comfortably below** the read timeout of its own callers — otherwise the service cannot fail fast: it and its callers time out at nearly the same instant, so callers abort (Istio DC) before the service can return a clean fast error. Add a **circuit breaker / fast-fail**, especially when the downstream sits on a hot path.

**Worked example (klara-prod 2026-06-30):** `jwt-service` had a **30s** client timeout to `luztenant-service` `/security-classes`, but its `luz-eletter`/`luz-eletter-dispatcher` callers also used a **30s** read timeout — a near-tie. When luztenant degraded, jwt held each request the full 30s and the callers aborted at the same moment, turning a transient dependency blip into minutes of caller-visible timeouts (196 caller aborts). A 5–8s timeout + breaker on the security-class call would have let jwt fail fast instead.

Rule of thumb: caller_timeout > service_handler_budget > Σ(downstream_timeouts), each with margin. Related: [[3 Resources/Infra/Observability/Cascading DC follow the timeout chain one layer down]], [[Luz caller read-timeout settings to jwt-service]].

## Related

- [[3 Resources/Infra/Observability/Cascading DC follow the timeout chain one layer down]]
- [[Luz caller read-timeout settings to jwt-service]]

%% ai-graph-start %%

**Related notes:**
- [[Cascading DC follow the timeout chain one layer down]]
- [[Luz caller read-timeout settings to jwt-service]]
- [[Istio DC response_flag with round latency = caller read timeout]]
- [[jwt-service token path synchronously calls luztenant security-classes]]
- [[Log red herrings enclosing class name and baseline-noise lines]]

%% ai-graph-end %%