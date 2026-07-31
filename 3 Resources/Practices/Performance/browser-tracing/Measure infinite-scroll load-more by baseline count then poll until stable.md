---
ai_hash: 1b0335d70f3e859e
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-21
entities: []
source: session 2026-07-21
status: seedling
tags:
- playwright
- performance
- infinite-scroll
- earchive
title: Measure infinite-scroll load-more by baseline count then poll until stable
type: howto
---

# Measure infinite-scroll load-more by baseline count then poll until stable

To measure an infinite-scroll "load more" batch (PrimeFaces `.ui-datascroller-item` list): before triggering the scroll, stamp an in-page mark `{base: currentItemCount, t0: performance.now()}`; then `scrollIntoView` the last item (plus `window.scrollTo(0, body.scrollHeight)` as fallback); then poll until the item count has been **stable for >2.5s** (or timeout). Report X = after − before, first-new-item time, and batch-complete time (last count change).

Why stability window, not a fixed wait: the batch renders incrementally, so "count increased once" is not "batch done"; and a fixed sleep either under-waits (miscounts X) or over-waits (inflates the time). The 2.5s quiet period is the settle detector.

Companion to [[Resettable MutationObserver harness measures skeleton-to-number appear time on SSR pages]] — same harness records the milestones.

## Related

- [[Resettable MutationObserver harness measures skeleton-to-number appear time on SSR pages]]

%% ai-graph-start %%

**Related notes:**
- [[Measure component render timing with Playwright addInitScript]]
- [[Resettable MutationObserver harness measures skeleton-to-number appear time on SSR pages]]
- [[Distinguish absent control from missed click when expanding lazy lists]]
- [[eArchive page DOM selectors (performance automation)]]
- [[Timing PrimeFaces dialog opens trusted click + stale-guard the reused dialog node]]

%% ai-graph-end %%