---
title: "data-tracking rate limiter caps global throughput because it reads request.client.host behind a proxy"
created: 2026-08-31
type: lesson
status: seedling
source: "session 2026-08-31 perf test"
tags: [leo-customer360, fastapi, rate-limiting, reverse-proxy, gotcha]
---

# data-tracking rate limiter caps global throughput because it reads request.client.host behind a proxy

The data-tracking-api rate limiter (`core/redis_cache.py` `allow_request`) keys its Redis window on `request.client.host` — the **immediate TCP peer**, NOT `X-Forwarded-For`. In UAT the app sits behind NLB -> Caddy -> nginx LB, and each hop opens a fresh upstream connection, so uvicorn always sees the **nginx LB container IP** (e.g. 172.18.0.5) as the client for EVERY request.

## Consequences
1. **Rate limit is global, not per-client.** All traffic shares one key `data-tracking-api:rate:ip:<lb-ip>`, so the effective cap is `TRACKING_RATE_LIMIT_REQUESTS` per `TRACKING_RATE_LIMIT_WINDOW_SECONDS` for the WHOLE service (default 120 / 60s), regardless of how many real clients there are. A load test bursting >120/min gets 429 + `Retry-After`.
2. **Per-IP abuse protection does not actually work** through the proxy chain — every real client looks like the LB.

## Fixes (if per-client limiting is desired)
- Run uvicorn with `--proxy-headers --forwarded-allow-ips=*` (or set the middleware) AND have the app read the client IP from `X-Forwarded-For` — but only trust it when the edge (Caddy) sets it and strips client-supplied values.
- For a load test that just needs throughput, temporarily raise `TRACKING_RATE_LIMIT_REQUESTS` on the box (env in `/opt/c360/tracking.env`) and restart the replicas.

Found while building `data-tracking-api/tests/perf_uat_tracking.py`. Related: [[Scale one uvicorn service into N replicas on one VM with a docker bridge + local nginx LB]].

## Related

- [[Scale one uvicorn service into N replicas on one VM with a docker bridge + local nginx LB]]
