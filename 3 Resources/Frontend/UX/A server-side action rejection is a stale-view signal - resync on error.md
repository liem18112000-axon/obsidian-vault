---
title: "A server-side action rejection is a stale-view signal - resync on error"
created: 2026-07-04
type: lesson
status: seedling
source: "session 2026-07-04, vinnstack stale approved PRD"
tags: [react, state-sync, error-handling, vinnstack]
---

# A server-side action rejection is a stale-view signal - resync on error

When a client action is rejected server-side (409-style: "already in progress", "already approved", idempotency lock held), the rejection usually means the server state DIFFERS from what the client is showing - often because another writer (a background run, a second tab, an API call) is mid-flight or already finished. A UI that only displays the error and keeps its cached record leaves the user staring at pre-action state even after the other writer completes ("I approved but the status didn't change").

Rule: in the action's error branch, re-fetch the affected record (and any list it appears in) alongside showing the error. Success paths naturally carry fresh state in the response; ERROR paths are where staleness hides, because nothing else triggers a reload.

Sibling symptom from the same incident: gating tabs computed from a slow-loading record appear seconds after the page paints - not a bug, but probe AFTER the record loads before diagnosing gating.

## Related

- [[Idempotency guards keyed on object presence break when hydration materializes the object]]
