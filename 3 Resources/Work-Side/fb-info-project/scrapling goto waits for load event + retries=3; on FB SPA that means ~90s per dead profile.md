---
ai_hash: b978aa8ec930f5c8
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-14
entities: []
source: code trace + live run 2026-06-14
status: seedling
tags:
- fb-info-project
- scrapling
- playwright
- performance
- gotcha
title: scrapling goto waits for load event + retries=3; on FB SPA that means ~90s
  per dead profile
type: lesson
---

# scrapling goto waits for load event + retries=3; on FB SPA that means ~90s per dead profile

Root cause of the slow profile-visit phase in fb-info-project (`src/profiles.py::visit_all`):

- scrapling's `AsyncDynamicSession.fetch` calls Playwright `page.goto(url)` **without `wait_until`**, so it uses Playwright's default `wait_until="load"` (see `scrapling/engines/_browsers/_controllers.py` ~line 353, then `_base.py::_wait_for_page_stability` does `wait_for_load_state("load")`).
- Facebook's SPA often **never fires the `load` event** (continuous lazy/XHR activity), so `goto` blocks the full `timeout` (set to 30000ms in profiles.py) and throws `Page.goto: Timeout 30000ms exceeded ... waiting until "load"`.
- scrapling then retries: its config default is **`retries=3`, `retry_delay=1`** (`scrapling/engines/_browsers/_validators.py:90`). So one hung profile costs ~3×30s ≈ **90 seconds** and still yields no data.

Knobs that ARE valid kwargs to AsyncDynamicSession (all fields of the validators Config struct): `timeout`, `retries`, `retry_delay`, `load_dom` (default True), `network_idle` (default False), `wait`, `wait_selector`, `max_pages`, `disable_resources`, `real_chrome`, `useragent`, `cookies`, `headless`.

Fix: lower `timeout` (e.g. 15000) and set `retries=1` to fail fast — cuts a dead profile from ~90s to ~15s (~6x). scrapling exposes **no** direct knob to change goto's `wait_until` to `domcontentloaded`, so timeout+retries is the practical lever. Some profiles still succeed quickly, so the timeouts are partly intermittent FB throttling of rapid sequential profile loads, not purely the load-event semantics.

## Related

- [[--max-expand caps comment batches not profile count; profile-visit phase dominates runtime]]

%% ai-graph-start %%

**Related notes:**
- [[--max-expand caps comment batches not profile count; profile-visit phase dominates runtime]]
- [[Large FB group posts expand huge but article-scan yields almost no profiles]]
- [[Scrapling AsyncDynamicSession reuses one browser with a max_pages tab pool for concurrent fetching]]
- [[Playwright click() auto-waits the full timeout on a missing locator; probe with count() first]]
- [[Distinguish absent control from missed click when expanding lazy lists]]

%% ai-graph-end %%