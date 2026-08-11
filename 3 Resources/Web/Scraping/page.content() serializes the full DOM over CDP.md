---
ai_hash: 42c404b5a60126c1
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-10
entities: []
source: code-review session 2026-06-10, fb-info-project
status: seedling
tags:
- playwright
- cdp
- performance
- scraping
title: page.content() serializes the full DOM over CDP
type: lesson
---

# page.content() serializes the full DOM over CDP

Playwright's `page.content()` serializes the entire in-memory DOM over CDP — on heavy pages (Facebook profiles) that is multi-MB of HTML and tens-to-hundreds of ms per call, even though it costs no extra network. When the value you want (e.g. a numeric uid) is already derivable from the URL (`profile.php?id=123`), skip the serialization and only fall back to HTML scraping for vanity URLs.

Same family of waste: acquiring an expensive resource for an empty work list — e.g. opening a browser session and immediately closing it because there were zero profiles to visit. Guard with an early return before the resource, not inside it.

## Related

- [[curl_cffi stream=True resolves redirects without downloading the body]]

%% ai-graph-start %%

**Related notes:**
- [[curl_cffi stream=True resolves redirects without downloading the body]]
- [[scrapling goto waits for load event + retries=3; on FB SPA that means ~90s per dead profile]]
- [[Use Playwright evaluate_all to batch-read element properties in one round-trip]]
- [[Playwright click() auto-waits the full timeout on a missing locator; probe with count() first]]
- [[Scrapling AsyncDynamicSession reuses one browser with a max_pages tab pool for concurrent fetching]]

%% ai-graph-end %%