---
title: "Mixing sync and async Playwright requires separate threads"
created: 2026-06-15
type: lesson
status: seedling
source: "fb-info-project Phase 3, 2026-06-15"
tags: [playwright, asyncio, threading, gotcha]
---

# Mixing sync and async Playwright requires separate threads

Playwright's **sync** API refuses to run in a thread that has a running asyncio event loop (it raises `It looks like you are using Playwright Sync API inside the asyncio loop`). So to run a sync-Playwright task and an async-Playwright task concurrently in one process, put them in **different threads**:

- sync Playwright (e.g. `DynamicFetcher.fetch` / `sync_playwright()`) → a worker thread with **no** asyncio loop.
- async Playwright (e.g. `AsyncDynamicSession` / `async_playwright()`) → another thread that owns the event loop (e.g. the main thread running `asyncio.run`).

Each launches its own browser process, so two Chromes are alive at once (more RAM). Bridge the two threads with a thread-safe `queue.Queue`.

**Why it comes up:** a producer/consumer scrape pipeline where collection uses the sync fetcher and profile-visiting uses the async session — see fb-info-project Phase 3 (`docs/phase3-producer-consumer-pipeline-progress.md`).

**Caveat / gotcha:** this is the *supported* arrangement, but mixing sync + async Playwright in one process is fragile across versions — validate with a real run, and if the two drivers conflict, fall back to running one side in a **subprocess** instead of a thread. Offline/mocked tests won't catch this because they stub the browser. Related: [[Producerconsumer overlap only saves min(producer, consumer) time]].

## Related

- [[Producerconsumer overlap only saves min(producer]]
- [[consumer) time]]
- [[Use Playwright evaluate_all to batch-read element properties in one round-trip]]
