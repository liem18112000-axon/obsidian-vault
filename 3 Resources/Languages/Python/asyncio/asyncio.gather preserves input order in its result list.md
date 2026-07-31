---
title: "asyncio.gather preserves input order in its result list"
created: 2026-06-11
type: concept
status: evergreen
source: "fb-info-project session 2026-06-11"
tags: [python, asyncio, concurrency]
---

# asyncio.gather preserves input order in its result list

`asyncio.gather(*aws)` returns results **in the order the awaitables were passed in**, regardless of which finishes first. Concurrency affects completion timing only, never result position.

Practical consequence: if a list must come out in a specific order after a concurrent fan-out (e.g. profiles sorted newest-comment-first before visiting each profile in parallel), it is sufficient to sort the *input* list — no re-sort or index bookkeeping is needed after gathering.
