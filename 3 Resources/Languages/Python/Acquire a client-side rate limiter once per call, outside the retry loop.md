---
title: "Acquire a client-side rate limiter once per call, outside the retry loop"
created: 2026-06-12
type: lesson
status: seedling
source: "session 2026-06-12, accesstrade_integration code review"
tags: [python, rate-limit, retry, gotcha, http]
---

# Acquire a client-side rate limiter once per call, outside the retry loop

**A client-side rate limiter must be acquired ONCE per logical request, before the retry loop — not inside it.** If `acquire()` sits inside the `while` that re-sends on 429/5xx/network errors, every retry re-reserves a slot and sleeps the full interval again, and (for a reservation-style limiter) pushes `next_allowed` further into the future each time.

Concrete failure (found in the Accesstrade transport): a 1-request-per-5-minute endpoint returns 429 with `Retry-After: 60`. Attempt 0 acquires (waits 0, reserves t+300). The code sleeps 60s, loops, and acquires AGAIN → waits ~240s more and reserves t+600. One retry adds ~5 idle minutes and doubles the budget burn; a burst of retries shoves all later calls out by multiples of the interval.

The retry path already has its own spacing — honor `Retry-After` / exponential backoff there. The rate limiter governs the *cadence of distinct calls*; the backoff governs *re-sends of one call*. They are different clocks and must not compound. Structure:

```python
if rate_limit_key:
    limiter.acquire(rate_limit_key)   # ONCE, before the loop
while True:
    resp = session.request(...)
    if retryable(resp) and attempt < max: sleep(retry_after or backoff); continue
    ...
```

This is the kind of bug tests with a fake clock won't catch unless they assert total sleep across a retried call. Relates to [[Client-side min-interval rate limiting via slot reservation]].

## Related

- [[Client-side min-interval rate limiting via slot reservation]]
- [[Affiliate API engineering best practices]]
