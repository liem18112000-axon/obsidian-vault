---
ai_hash: 32e4ffdb78e17953
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-20
entities: []
source: session 2026-07-20
status: seedling
tags:
- linkedin
- scraping
- playwright
- http-999
title: LinkedIn profiles cannot be fetched anonymously
type: observation
---

# LinkedIn profiles cannot be fetched anonymously

LinkedIn profile pages are effectively unscrapeable without a logged-in session: a plain HTTP fetch (WebFetch/curl) returns HTTP 999 (LinkedIn's anti-bot status), and a fresh Playwright browser gets redirected to the sign-up authwall (`linkedin.com/authwall`). Web-searching the person is also unreliable for specifics. The only working paths are a browser profile that is already logged in, or asking the person for the data directly. Plan CV/profile tasks around asking the user for dates/titles instead of expecting to pull them from LinkedIn.

%% ai-graph-start %%

**Related notes:**
- _(none above threshold)_

%% ai-graph-end %%