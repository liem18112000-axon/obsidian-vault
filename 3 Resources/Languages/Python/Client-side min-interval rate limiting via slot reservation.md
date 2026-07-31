---
title: "Client-side min-interval rate limiting via slot reservation"
created: 2026-06-11
type: howto
status: seedling
source: "session 2026-06-11, accesstrade_integration repo"
tags: [python, rate-limit, concurrency, testing]
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
