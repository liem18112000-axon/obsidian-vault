---
title: "page.content() serializes the full DOM over CDP"
created: 2026-06-10
type: lesson
status: seedling
source: "code-review session 2026-06-10, fb-info-project"
tags: [playwright, cdp, performance, scraping]
---

# page.content() serializes the full DOM over CDP

Playwright's `page.content()` serializes the entire in-memory DOM over CDP — on heavy pages (Facebook profiles) that is multi-MB of HTML and tens-to-hundreds of ms per call, even though it costs no extra network. When the value you want (e.g. a numeric uid) is already derivable from the URL (`profile.php?id=123`), skip the serialization and only fall back to HTML scraping for vanity URLs.

Same family of waste: acquiring an expensive resource for an empty work list — e.g. opening a browser session and immediately closing it because there were zero profiles to visit. Guard with an early return before the resource, not inside it.

## Related

- [[curl_cffi stream=True resolves redirects without downloading the body]]
