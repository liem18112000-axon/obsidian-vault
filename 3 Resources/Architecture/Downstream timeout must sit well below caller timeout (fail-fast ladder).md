---
title: "Downstream timeout must sit well below caller timeout (fail-fast ladder)"
created: 2026-06-30
type: lesson
status: seedling
source: "PROD jwt-service investigation 2026-06-30"
tags: [timeout, resilience, circuit-breaker, microservices, klara]
---

# Downstream timeout must sit well below caller timeout (fail-fast ladder)

A services client-side timeout on a downstream call must sit **comfortably below** the read timeout of its own callers — otherwise the service cannot fail fast: it and its callers time out at nearly the same instant, so callers abort (Istio DC) before the service can return a clean fast error. Add a **circuit breaker / fast-fail**, especially when the downstream sits on a hot path.

**Worked example (klara-prod 2026-06-30):** `jwt-service` had a **30s** client timeout to `luztenant-service` `/security-classes`, but its `luz-eletter`/`luz-eletter-dispatcher` callers also used a **30s** read timeout — a near-tie. When luztenant degraded, jwt held each request the full 30s and the callers aborted at the same moment, turning a transient dependency blip into minutes of caller-visible timeouts (196 caller aborts). A 5–8s timeout + breaker on the security-class call would have let jwt fail fast instead.

Rule of thumb: caller_timeout > service_handler_budget > Σ(downstream_timeouts), each with margin. Related: [[Cascading DC: follow the timeout chain one layer down]], [[Luz caller read-timeout settings to jwt-service]].

## Related

- [[Cascading DC: follow the timeout chain one layer down]]
- [[Luz caller read-timeout settings to jwt-service]]
