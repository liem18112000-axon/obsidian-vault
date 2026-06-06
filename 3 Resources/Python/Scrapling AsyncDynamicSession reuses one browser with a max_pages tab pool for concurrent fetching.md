---
title: "Scrapling AsyncDynamicSession reuses one browser with a max_pages tab pool for concurrent fetching"
created: 2026-06-05
type: howto
status: seedling
source: "session 2026-06-05"
tags: [python, scrapling, asyncio, playwright, concurrency, scraping, fb-info-project]
---

# Scrapling AsyncDynamicSession reuses one browser with a max_pages tab pool for concurrent fetching

Scrapling's per-call `DynamicFetcher.fetch()` cold-starts and tears down a whole browser every call — ~3-5s of pure overhead per URL. For many URLs, switch to `AsyncDynamicSession`: it holds **one** persistent browser context open and rotates requests through a `PagePool` capped at `max_pages` tabs, so `max_pages` URLs are fetched concurrently (≈ that many × throughput).

```python
async with AsyncDynamicSession(max_pages=5, headless=False, real_chrome=True,
                               useragent=UA, cookies=cookies,
                               disable_resources=True, timeout=30_000) as session:
    sem = asyncio.Semaphore(5)
    async def visit(u):
        async with sem:
            return await session.fetch(u, page_action=my_async_action)
    results = await asyncio.gather(*(visit(u) for u in urls))
```

Key gotchas learned (scrapling 0.x, 2026-06):
- **Gate submissions with `Semaphore(max_pages)`.** Without it, `gather`-ing N>max_pages tasks makes the surplus tasks spin in the pool's internal wait loop, which raises `TimeoutError` after `_max_wait_for_page` (default **60s**) if slots don't free in time. The semaphore keeps exactly max_pages `fetch()` calls in flight so the pool never blocks.
- In the async session, **`page_action` is awaited** — its body must use `await page.inner_text(...)`, `await asyncio.sleep(...)`, `await page.mouse.wheel(...)`. The sync session's plain-function page_action won't port directly.
- **`disable_resources=True`** drops image/css/font/media/websocket requests — free speedup when you only read `inner_text`.
- Constructor params (cookies, useragent, real_chrome, timeout, disable_resources) are set once on the session, not per fetch.

Trade-off: concurrency means many rapid requests from one logged-in session — for sites with bot detection (e.g. Facebook) this raises checkpoint/block risk, so use a throwaway account or keep per-tab jitter.

## Related

- [[scrapling[fetchers] extra pins playwright exactly - install deps individually to keep your own pin]]
