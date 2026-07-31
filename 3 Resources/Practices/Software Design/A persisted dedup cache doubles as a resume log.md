---
title: "A persisted dedup cache doubles as a resume log"
created: 2026-06-15
type: lesson
status: seedling
source: "fb-info-project pause/resume, 2026-06-15"
tags: [resume, caching, resilience, batch-processing, design-pattern]
---

# A persisted dedup cache doubles as a resume log

A memoization or dedup cache keyed by work-item identity is, structurally, already a record of *what has been done* — so persisting it and re-seeding it on the next run gives you resume **for free**, without writing separate skip-this-item logic.

**Why it works:** if a run already has a fast-path that reuses a cached result instead of redoing the work, then loading a prior run's cache makes every already-done item a cache hit on restart. The skip logic you would otherwise hand-write *is* the cache lookup.

**Concrete case (fb-info-project scraper):** a run-wide `{profile_url: extracted_fields}` cache existed for cross-link dedup (a commenter appearing under several posts is fetched once). To add profile-level pause/resume across process restarts, I persisted that cache to a checkpoint JSON and seeded the next run's cache from it. Already-visited profiles came back as cache hits — no re-fetch, no re-work — purely by reusing the dedup path.

**When to reach for it:** any long batch job that (a) already dedups/memoizes by a stable key and (b) needs to survive interruption. Ask 'is my dedup cache also my done-set?' — often it is.

Caveat: only persist entries that represent genuinely-completed work — see [[Cache only successful results so failures retry on resume]].

Related: [[A resume must not re-charge one-time accounting]], [[3 Resources/Practices/Software Design/Checkpoint files atomic tmp+rename write plus an input fingerprint]].

## Related

- [[Cache only successful results so failures retry on resume]]
- [[A resume must not re-charge one-time accounting]]
- [[3 Resources/Practices/Software Design/Checkpoint files atomic tmp+rename write plus an input fingerprint]]
