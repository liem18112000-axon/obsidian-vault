---
title: "fb-info-project duplicates FB-fragile selectors across get_locations.py and scrapling_test.py"
created: 2026-06-05
type: lesson
status: seedling
source: "session 2026-06-05"
tags: [facebook, scraping, duplication, gotcha, fb-info-project]
---

# fb-info-project duplicates FB-fragile selectors across get_locations.py and scrapling_test.py

Inlining the shared Facebook scraping config into `scrapling_test.py` made it standalone, but the layout-fragile pieces now live in **two** files — when Facebook changes its DOM or labels, both `get_locations.py` and `scrapling_test.py` must be patched, or the Scrapling-vs-Playwright comparison silently drifts.

The duplicated fragile set: bilingual (VI/EN) location regexes (`_CITY_RE`, `_HOMETOWN_RE`, line-anchored `_FROM_VI_RE`), phone regexes, comment-sort button labels (`_SORT_SELECTOR_RE`, `_ALL_COMMENTS_RE`), and the `_SKIP_HREF`/`_SKIP_NAMES` noise filters.

**Why duplication over a shared module:** `get_locations.py` has import-time side effects (re-wraps `sys.stdout`/`sys.stderr` for UTF-8) and heavy deps (`ollama`, `dotenv`). Verbatim copies keep the comparison script zero-coupled and the two files diffable against each other. The cost is the dual-update rule above — accepted deliberately (2026-06-05).

> [!info] Resolved — 2026-06-05 (later the same day)
> `get_locations.py` was **deleted**; the project pivoted to `scrapling_test.py` as the sole script (deps trimmed to `playwright`, `scrapling` + its 4 fetcher deps; `ollama`/`python-dotenv` dropped). The dual-update gotcha no longer applies — the fragile selector set above now lives only in `scrapling_test.py`, and that list remains the *check-first* map when Facebook changes its DOM/labels. Watch for stragglers: constants not in the original inline set (e.g. `_MAX_STALE`) surfaced as unresolved references after the deletion and had to be re-inlined.
