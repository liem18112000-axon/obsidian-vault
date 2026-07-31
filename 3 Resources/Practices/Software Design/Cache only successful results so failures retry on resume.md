---
title: "Cache only successful results so failures retry on resume"
created: 2026-06-15
type: lesson
status: seedling
source: "fb-info-project pause/resume, 2026-06-15"
tags: [resume, caching, error-handling, resilience]
---

# Cache only successful results so failures retry on resume

When a cache is reused as a resume/done-set (see [[A persisted dedup cache doubles as a resume log]]), the rule **'never cache a failed operation'** quietly delivers correct retry-on-resume semantics — failed items are simply absent from the seeded cache, so they are re-attempted next run, while genuinely-completed items are skipped.

**The two cases it separates:**
- *Successful operation with an empty/negative result* (e.g. a profile fetched fine but has no location) → cached → on resume it is correctly treated as done and skipped. Re-doing it would just produce the same empty result.
- *Failed operation* (e.g. fetch raised mid network-disconnect) → NOT cached → on resume it is absent and retried. This is exactly what a disconnect needs.

**Key point:** the invariant 'a fetch that raised is never cached' was originally there to stop a transient error from poisoning a later reuse within the same run. The *same* invariant, once the cache is persisted, gives free disconnect-resume correctness — one rule, two payoffs. Distinguish 'completed-but-empty' from 'failed' by whether the operation threw, not by whether the result is empty.

Related: [[A persisted dedup cache doubles as a resume log]]

## Related

- [[A persisted dedup cache doubles as a resume log]]
