---
title: "Health checks should probe dependencies and split critical vs fail-open"
created: 2026-09-05
type: concept
status: seedling
source: "session 2026-09-05, leo-customer360 ads + tracking"
tags: [health-check, readiness, liveness, fastapi, resilience, ops]
---

# Health checks should probe dependencies and split critical vs fail-open

A health/readiness endpoint that only reports **liveness** ("the process is running") reads as healthy even when a backing service is down, so deploy gates and container orchestrators keep routing traffic to a box that cannot actually serve. Make health endpoints **probe their real dependencies** and reflect the result in the HTTP status code.

**Critical vs fail-open classification** — not every dependency should fail the check:
- **Critical dependency** (the service cannot do its job without it): probe it; on failure return **503** with `status:error` and which dep is `unreachable`. e.g. the DB for an API, the S3 sink for an ingestion service.
- **Fail-open dependency** (the service degrades but still works without it): probe it but keep **200**, reporting `status:degraded` + the dep as `unreachable`. e.g. a Redis rate-limiter/cache that the app is designed to bypass when down.

**Implementation notes:**
- The probe must **catch its own exceptions and return a bool/status**, not let them bubble — otherwise a down dependency yields an unhandled **500** instead of an informative 503. (Pattern: a graceful `_reachable() -> bool` that the strict startup check wraps and re-raises.)
- In FastAPI, inject the dependency clients with `Depends(get_x)` so tests can override them via `app.dependency_overrides` and assert ok/degraded/error without real infra. Set the code with an injected `response: Response` (`response.status_code = 503`).
- Keep the probe cheap (`SELECT 1`, S3 `list_buckets`, Redis `ping`) since the container healthcheck calls it on an interval.

## Related

- [[leo-customer360 service dependency + health-probe map]]
