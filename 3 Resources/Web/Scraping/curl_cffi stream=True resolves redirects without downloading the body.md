---
ai_hash: cbd6a3aecd05d5b1
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-10
entities: []
source: code-review session 2026-06-10, fb-info-project
status: seedling
tags:
- curl-cffi
- http
- redirects
- efficiency
title: curl_cffi stream=True resolves redirects without downloading the body
type: howto
---

# curl_cffi stream=True resolves redirects without downloading the body

When only the final URL of a redirect chain matters (resolving share links, bare ids, shorteners), `curl_cffi`'s `requests.get(url, allow_redirects=True, stream=True)` follows the redirects but never transfers the body — read `response.url` and `response.close()`. Without `stream=True` the full final page (hundreds of KB+) is downloaded just to be discarded.

`HEAD` (`requests.head`) also skips the body via `CURLOPT_NOBODY`, but some sites (Facebook among them) answer HEAD differently or not at all, so streamed GET is the reliable choice. Pair with `impersonate='chrome'` + session cookies when the site bounces unknown clients to a login page.

## Related

- [[SameSite=None cookies require Secure or Chromium drops them]]

%% ai-graph-start %%

**Related notes:**
- [[page.content() serializes the full DOM over CDP]]
- [[Facebook sharev links can resolve to reels — classify after the redirect]]

%% ai-graph-end %%