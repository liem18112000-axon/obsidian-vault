---
title: "Distinguish absent control from missed click when expanding lazy lists"
created: 2026-06-30
type: lesson
status: seedling
source: "fb-info-project --thorough mode, session 2026-06-30"
tags: [scraping, playwright, facebook, gotcha, completeness]
---

# Distinguish absent control from missed click when expanding lazy lists

When a scraper expands a lazily-loaded list by clicking a "view more" control (e.g. Facebook comments/replies), a click that returns failure is **ambiguous**:

- (a) the control is **gone** — the list is fully expanded (truly done), or
- (b) the control is **still present** but the click missed it because it was off-screen or covered by an overlay.

Treating case (b) as "done" silently **truncates** the results — this is a common reason a scraper reports far fewer items than the page actually has.

**Fix:** after a failed click, query whether the control's locator still matches any element (`count() > 0`). If present, scroll it toward the viewport and **retry**, bounded by a `STUCK_RETRY` counter so a genuinely unclickable duplicate/clone can't spin the loop forever. Only count the round as **stale** (toward the give-up threshold) when **no** control is present.

**Companion completeness levers** (trade wall-clock for capture):
- raise the consecutive-empty-round limit before declaring a pass exhausted, so a slow lazy-load isn't mistaken for "no more items";
- lengthen the settle pause after each expand so freshly loaded nodes render before the next round is judged empty.

In fb-info-project these are gated behind a `--thorough` flag (cli → batch → collect_profiles → collect); default stays fast. Implemented in `src/collector.py` with a `_count()` helper distinguishing the two failure cases.

## Related
[[fb-info-project]]

## Related

- [[fb-info-project]]
