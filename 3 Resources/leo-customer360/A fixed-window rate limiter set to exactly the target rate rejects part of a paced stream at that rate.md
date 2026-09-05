---
title: "A fixed-window rate limiter set to exactly the target rate rejects part of a paced stream at that rate"
created: 2026-08-31
type: lesson
status: seedling
source: "session 2026-08-31 10rps trial"
tags: [rate-limiting, fixed-window, load-testing, gotcha, leo-customer360]
---

# A fixed-window rate limiter set to exactly the target rate rejects part of a paced stream at that rate

A fixed-window rate limiter (Redis `INCR` key with `EXPIRE=window` on first hit — the pattern in data-tracking-api `core/redis_cache.py`) set to a limit N per window W **cannot cleanly pass an offered load of exactly N/W requests per second.** A client paced at the target rate does not align to the server window boundaries, so some windows receive N+1..N+k requests and the overflow is rejected.

## Evidence
Limiter = 10 req / 1s. Client paced at 10 RPS (200 requests). Result: 184/200 accepted, **16x 429** (~8% loss) — purely window-edge jitter, not capacity.

## Takeaways
- To ALLOW a sustained rate R, set the limiter ABOVE R (headroom), or use a longer window with proportional budget (e.g. 10R per 10s absorbs per-second bursts better), or a sliding-window / token-bucket limiter instead of fixed-window.
- To load-test SERVICE capacity by ramping offered RPS, raise the limiter ONCE to a value far above any tested RPS so the limiter never interferes — do NOT set the limiter to the RPS you are testing (it becomes the bottleneck and you measure the limiter, not the service).

Found tuning `data-tracking-api/tests/perf_uat_tracking.py` on UAT. Related: [[data-tracking rate limiter caps global throughput because it reads request.client.host behind a proxy]].

## Related

- [[data-tracking rate limiter caps global throughput because it reads request.client.host behind a proxy]]
