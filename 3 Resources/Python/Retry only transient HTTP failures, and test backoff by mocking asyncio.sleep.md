---
title: "Retry only transient HTTP failures, and test backoff by mocking asyncio.sleep"
created: 2026-08-27
type: lesson
status: seedling
source: "session 2026-08-27 — kga atlassian.py"
tags: [retry, httpx, testing, backoff, python]
---

# Retry only transient HTTP failures, and test backoff by mocking asyncio.sleep

When adding retry to an HTTP client, retry **only transient failures** and **fail fast on the rest**:
- Retry: network/timeout errors (`httpx.TransportError`) and HTTP **429 / 5xx**.
- Do NOT retry 4xx like 401/403/404 — they will never succeed, so retrying just wastes 3× the latency. A naive "retry on any exception" turns a 404 into a 7-second hang.

Hand-rolled backoff is ~10 lines and needs no dependency (tenacity is overkill when you also need a retry predicate anyway): loop `range(len(backoffs)+1)`; on a retryable error and not-last attempt, `await asyncio.sleep(backoffs[attempt])`; else re-raise. Backoffs `(1, 2, 4)` = 3 retries.

**Testing gotcha:** make the backoffs injectable AND `monkeypatch.setattr("mod.asyncio.sleep", fake_async_sleep)` where `fake_async_sleep` appends the delay to a list. This (a) makes the suite instant instead of waiting 7s, and (b) lets you **assert the exact schedule** (`sleeps == [1.0, 2.0, 4.0]`) and the call count (initial + N retries). Use `httpx.MockTransport(handler)` with a stateful handler (fail K times, then 200) to drive it.

Context: kga `atlassian.py` (LUZ-159671 test-agent), read-only client.

## Related

- [[Claude on Vertex AI uses anthropic[vertex]]]
- [[not google-genai]]
