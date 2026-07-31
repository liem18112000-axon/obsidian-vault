---
title: "Measure infinite-scroll load-more by baseline count then poll until stable"
created: 2026-07-21
type: howto
status: seedling
source: "session 2026-07-21"
tags: [playwright, performance, infinite-scroll, earchive]
---

# Measure infinite-scroll load-more by baseline count then poll until stable

To measure an infinite-scroll "load more" batch (PrimeFaces `.ui-datascroller-item` list): before triggering the scroll, stamp an in-page mark `{base: currentItemCount, t0: performance.now()}`; then `scrollIntoView` the last item (plus `window.scrollTo(0, body.scrollHeight)` as fallback); then poll until the item count has been **stable for >2.5s** (or timeout). Report X = after − before, first-new-item time, and batch-complete time (last count change).

Why stability window, not a fixed wait: the batch renders incrementally, so "count increased once" is not "batch done"; and a fixed sleep either under-waits (miscounts X) or over-waits (inflates the time). The 2.5s quiet period is the settle detector.

Companion to [[Resettable MutationObserver harness measures skeleton-to-number appear time on SSR pages]] — same harness records the milestones.

## Related

- [[Resettable MutationObserver harness measures skeleton-to-number appear time on SSR pages]]
