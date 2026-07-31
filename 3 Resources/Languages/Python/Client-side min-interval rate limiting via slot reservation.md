---
ai_hash: 7b3e239e29627f84
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-11
entities: []
source: session 2026-06-11, accesstrade_integration repo
status: seedling
tags:
- python
- rate-limit
- concurrency
- testing
title: Client-side min-interval rate limiting via slot reservation
type: howto
---

# Client-side min-interval rate limiting via slot reservation

**To honor a hard API cadence (e.g. 1 request per 5 minutes) client-side, use a reservation-based limiter: under a lock, compute your slot as `max(now, next_allowed[key])` and advance `next_allowed[key] = slot + interval`; then sleep until your slot *outside* the lock.** Each caller reserves the next free slot atomically, so concurrent threads queue at exact interval spacing instead of racing, and unrelated keys never block each other (the lock only guards bookkeeping, never the sleep).

```python
with self._lock:
    interval = self._intervals.get(key, 0.0)
    now = self._clock()
    scheduled = max(now, self._next_allowed.get(key, now))
    if interval > 0:
        self._next_allowed[key] = scheduled + interval
wait = scheduled - now
if wait > 0:
    self._sleep(wait)
```

Inject `clock` and `sleep` as constructor params — tests then verify spacing with a fake clock in microseconds instead of real minutes. Used in `api_services/rate_limit.py` to enforce the OBS conversion-report cadence documented in [[Accesstrade API rate limits and pagination]].

## Related

- [[Accesstrade API rate limits and pagination]]
- [[Affiliate API engineering best practices]]

%% ai-graph-start %%

**Related notes:**
- [[Acquire a client-side rate limiter once per call, outside the retry loop]]
- [[Affiliate API engineering best practices]]
- [[Accesstrade API rate limits and pagination]]
- [[Bound ThreadPoolExecutor + budget keeps per-item LLM scoring inside a web request window]]

%% ai-graph-end %%