---
ai_hash: fae75130966d1693
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-05
entities:
- fb-info-project
- FB-fragile selectors
- get_locations.py
- scrapling_test.py
- Facebook
- DOM
- labels
- Scrapling
- Playwright
- _CITY_RE
- _HOMETOWN_RE
- _FROM_VI_RE
- phone regexes
- _SORT_SELECTOR_RE
- _ALL_COMMENTS_RE
- _SKIP_HREF
- _SKIP_NAMES
- sys.stdout
- sys.stderr
- UTF-8
- ollama
- python-dotenv
- dual-update rule
- _MAX_STALE
- '2026-06-05'
- Facebook scraping configuration
- import-time side effects
- heavy dependencies
- comparison script
- fragile selector set
- unresolved references
source: session 2026-06-05
status: seedling
tags:
- facebook
- scraping
- duplication
- gotcha
- fb-info-project
title: fb-info-project duplicates FB-fragile selectors across get_locations.py and
  scrapling_test.py
type: lesson
---

# fb-info-project duplicates FB-fragile selectors across get_locations.py and scrapling_test.py

Inlining the shared Facebook scraping config into `scrapling_test.py` made it standalone, but the layout-fragile pieces now live in **two** files — when Facebook changes its DOM or labels, both `get_locations.py` and `scrapling_test.py` must be patched, or the Scrapling-vs-Playwright comparison silently drifts.

The duplicated fragile set: bilingual (VI/EN) location regexes (`_CITY_RE`, `_HOMETOWN_RE`, line-anchored `_FROM_VI_RE`), phone regexes, comment-sort button labels (`_SORT_SELECTOR_RE`, `_ALL_COMMENTS_RE`), and the `_SKIP_HREF`/`_SKIP_NAMES` noise filters.

**Why duplication over a shared module:** `get_locations.py` has import-time side effects (re-wraps `sys.stdout`/`sys.stderr` for UTF-8) and heavy deps (`ollama`, `dotenv`). Verbatim copies keep the comparison script zero-coupled and the two files diffable against each other. The cost is the dual-update rule above — accepted deliberately (2026-06-05).

> [!info] Resolved — 2026-06-05 (later the same day)
> `get_locations.py` was **deleted**; the project pivoted to `scrapling_test.py` as the sole script (deps trimmed to `playwright`, `scrapling` + its 4 fetcher deps; `ollama`/`python-dotenv` dropped). The dual-update gotcha no longer applies — the fragile selector set above now lives only in `scrapling_test.py`, and that list remains the *check-first* map when Facebook changes its DOM/labels. Watch for stragglers: constants not in the original inline set (e.g. `_MAX_STALE`) surfaced as unresolved references after the deletion and had to be re-inlined.

%% ai-graph-start %%

**Related notes:**
- [[scrapling[fetchers] extra pins playwright exactly - install deps individually to keep your own pin]]
- [[Self-healing scraper selectors — LLM fallback only on verified failure, then cache]]
- [[scrapling goto waits for load event + retries=3; on FB SPA that means ~90s per dead profile]]
- [[Unanchored 'From' regex captures the profile name from Facebook's 'See more from' buttons]]
- [[Facebook post permalinks render the post twice — dialog plus a hidden page copy]]

**Relations:**
- fb-info-project — *duplicates* — FB-fragile selectors
- FB-fragile selectors — *is duplicated across* — get_locations.py
- FB-fragile selectors — *is duplicated across* — scrapling_test.py
- scrapling_test.py — *contains* — Facebook scraping configuration
- Facebook scraping configuration — *is* — standalone
- Facebook — *changes* — DOM
- Facebook — *changes* — labels
- get_locations.py — *requires patching when* — Facebook changes DOM
- get_locations.py — *requires patching when* — Facebook changes labels
- scrapling_test.py — *requires patching when* — Facebook changes DOM
- scrapling_test.py — *requires patching when* — Facebook changes labels
- Scrapling — *compared with* — Playwright
- fragile selector set — *includes* — _CITY_RE
- fragile selector set — *includes* — _HOMETOWN_RE
- fragile selector set — *includes* — _FROM_VI_RE
- fragile selector set — *includes* — phone regexes
- fragile selector set — *includes* — _SORT_SELECTOR_RE
- fragile selector set — *includes* — _ALL_COMMENTS_RE
- fragile selector set — *includes* — _SKIP_HREF
- fragile selector set — *includes* — _SKIP_NAMES
- get_locations.py — *has* — import-time side effects
- import-time side effects — *re-wraps* — sys.stdout
- import-time side effects — *re-wraps* — sys.stderr
- sys.stdout — *for* — UTF-8
- sys.stderr — *for* — UTF-8
- get_locations.py — *has* — heavy dependencies
- heavy dependencies — *include* — ollama
- heavy dependencies — *include* — python-dotenv
- Verbatim copies — *keep* — comparison script zero-coupled
- Verbatim copies — *keep* — get_locations.py diffable
- Verbatim copies — *keep* — scrapling_test.py diffable
- duplication — *leads to* — dual-update rule
- dual-update rule — *accepted on* — 2026-06-05
- get_locations.py — *was deleted on* — 2026-06-05
- project — *pivoted to* — scrapling_test.py
- scrapling_test.py — *became* — sole script
- scrapling_test.py — *dependencies trimmed to* — Playwright
- scrapling_test.py — *dependencies trimmed to* — Scrapling
- Scrapling — *has* — 4 fetcher dependencies
- ollama — *was dropped from* — scrapling_test.py
- python-dotenv — *was dropped from* — scrapling_test.py
- dual-update rule — *no longer applies* — 2026-06-05
- fragile selector set — *lives only in* — scrapling_test.py
- fragile selector set — *is* — check-first map
- check-first map — *for* — Facebook changes DOM
- check-first map — *for* — Facebook changes labels
- _MAX_STALE — *surfaced as* — unresolved references
- _MAX_STALE — *was* — re-inlined

%% ai-graph-end %%