---
title: "Never cache a negative fallback in the same slot as a resolved value"
created: 2026-07-01
type: lesson
status: seedling
source: "code review 2026-07-01 (vinnstack)"
tags: [memoization, caching, gotcha, resolver]
---

# Never cache a negative fallback in the same slot as a resolved value

A resolver that memoizes into a module-level variable gated by `if (cached) return cached` must cache **only a positively-confirmed result** — never the give-up/fallback sentinel written into that same slot.

**Why it bites:** if the fall-through path does `cached = "claude"` (the bare-name last resort) in the same variable, a single *transient* miss permanently pins a long-lived process to the failure value. Once cached, `if (cached) return cached` short-circuits every future call, so no re-scan ever happens until the process restarts.

**Trigger:** the first lookup runs before the resource is discoverable — a global npm dependency still installing, PATH not yet refreshed for the running process, an install dir absent from the launcher's PATH. The miss gets memoized; even after the user fixes the install, every call keeps returning the stale failure.

**Fix:** leave the cache `null` on fall-through so the next call re-scans; only assign the cache when you have an `existsSync`-confirmed (or otherwise validated) real value. Distinguish "resolved successfully" from "gave up" — don't conflate them in one truthy slot.

Real example: `resolveClaudeBin()` in vinnstack (`lib/ultracodeRunner.ts`) cached the `"claude"` last-resort into `cachedClaudeBin`, locking the chat into permanent ENOENT on Windows.

Related: negative-result caching (caching a *miss* at all) is a separate decision — sometimes you want it with a TTL, but never silently forever in the same slot as hits.

## Related

- [[Node.js process.env is case-insensitive on Windows]]
