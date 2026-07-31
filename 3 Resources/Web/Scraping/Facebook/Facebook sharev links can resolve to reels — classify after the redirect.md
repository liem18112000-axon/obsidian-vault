---
ai_hash: 2452dbcaebc55a80
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-13
entities: []
source: session 2026-06-13 fb-info-project share/v misclassification
status: seedling
tags:
- facebook
- scraping
- gotcha
- reels
- url-redirect
title: Facebook /share/v/ links can resolve to reels — classify after the redirect
type: gotcha
---

# Facebook /share/v/ links can resolve to reels — classify after the redirect

A Facebook `/share/v/<code>/` link (the `v` nominally meaning "video") does NOT reliably point to a video — it can 302-redirect to a **reel** (e.g. `/share/v/1KVtDop6j6/` → `/reel/988246677276789`). The same unreliability applies to the `/share/<letter>/` family generally: the letter (v/r/p) is only a weak hint, not the true content type.

**Consequence for scraping:** classifying the scrape mode from the raw share URL's path text misclassifies these — a reel-behind-`/share/v/` gets treated as a video, so the reel comment side-panel is never opened and zero comments are collected.

**Fix / rule:** resolve the 302 redirect to the canonical URL FIRST (e.g. via curl_cffi with session cookies + browser TLS impersonation), then classify the resolved URL. In fb-info-project this meant calling `resolve_redirect()` in `service.batch()` for any `/share/` or `fb.watch/` link, not only for bare-numeric-id links. Reels also need URL normalization afterward to strip the `?rdid=&share_url=` query the redirect appends.

Related: [[Scroll Facebook reel comments via JS, never mouse.wheel]] — both are reel-handling gotchas in the same scraper.

## Related

- [[3 Resources/Web/Playwright/Scroll Facebook reel comments via JS, never mouse.wheel]]

%% ai-graph-start %%

**Related notes:**
- [[Facebook reel comments are hidden behind the comment icon]]
- [[Scroll Facebook reel comments via JS, never mouse.wheel]]
- [[Facebook post permalinks render the post twice — dialog plus a hidden page copy]]
- [[Resolving a wikilink by basename truncates titles containing a slash]]
- [[FB photofbid= links scrape as post mode; filename id falls back to na]]

%% ai-graph-end %%