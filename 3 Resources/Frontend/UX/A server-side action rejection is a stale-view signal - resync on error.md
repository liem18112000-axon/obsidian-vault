---
ai_hash: 0529023c8a3f4f01
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-04
entities: []
source: session 2026-07-04, vinnstack stale approved PRD
status: seedling
tags:
- react
- state-sync
- error-handling
- vinnstack
title: A server-side action rejection is a stale-view signal - resync on error
type: lesson
---

# A server-side action rejection is a stale-view signal - resync on error

When a client action is rejected server-side (409-style: "already in progress", "already approved", idempotency lock held), the rejection usually means the server state DIFFERS from what the client is showing - often because another writer (a background run, a second tab, an API call) is mid-flight or already finished. A UI that only displays the error and keeps its cached record leaves the user staring at pre-action state even after the other writer completes ("I approved but the status didn't change").

Rule: in the action's error branch, re-fetch the affected record (and any list it appears in) alongside showing the error. Success paths naturally carry fresh state in the response; ERROR paths are where staleness hides, because nothing else triggers a reload.

Sibling symptom from the same incident: gating tabs computed from a slow-loading record appear seconds after the page paints - not a bug, but probe AFTER the record loads before diagnosing gating.

## Related

- [[Idempotency guards keyed on object presence break when hydration materializes the object]]

%% ai-graph-start %%

**Related notes:**
- [[Idempotency guards keyed on object presence break when hydration materializes the object]]
- [[Whole-aggregate read-modify-write for a per-child toggle causes lost updates under concurrent sibling writes]]
- [[Per-account write silently skipped when the server cant resolve the session looks saved, isnt]]
- [[Secondary-write failures should fail loud when their silent version was the actual bug]]
- [[Classify stream failures on the server, not the client]]

%% ai-graph-end %%