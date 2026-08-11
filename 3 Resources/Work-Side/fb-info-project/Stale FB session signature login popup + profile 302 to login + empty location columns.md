---
ai_hash: 0aa01a791b6a7cab
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-14
entities: []
source: live run 2026-06-14
status: seedling
tags:
- fb-info-project
- facebook
- session
- debugging
title: 'Stale FB session signature: login popup + profile 302 to /login + empty location
  columns'
type: lesson
---

# Stale FB session signature: login popup + profile 302 to /login + empty location columns

Symptom cluster that means the saved Facebook session in `session/fb_session.json` is **expired/insufficient**, even when `is_authenticated()` passes (i.e. `c_user` + `xs` cookies are present but stale):

1. Startup warning: `login popup detected — session may be expired`.
2. During profile visits, the fetcher logs `Fetched (302) GET .../<profile>` immediately followed by `Fetched (200) GET .../login/?next=...` — Facebook bounced the request to the login page.
3. The scraped page title comes back as the generic `"Facebook"` instead of the real post/profile title.
4. Output rows are produced (names + profile URLs collected from comments) but every `Nơi ở hoặc vị trí` / `Quê quán` / `Số điện thoại` column is empty, and the run logs `N row(s), 0 with a location`.

`c_user`/`xs` being present is necessary but **not sufficient** — they expire. Fix: re-export fresh facebook.com cookies (Cookie-Editor -> Export as JSON) into `session/fb_session.json`. Note not all empty-location rows mean a dead session — a logged-in run can still get blanks from privacy-restricted profiles; it's the 302->/login pattern that proves the session, not the page, is the problem.

## Related

- [[3 Resources/Work-Side/fb-info-project/FB photofbid= links scrape as post mode; filename id falls back to na]]

%% ai-graph-start %%

**Related notes:**
- [[Facebook shows a See more on Facebook login dialog when the session is logged out]]
- [[FB photofbid= links scrape as post mode; filename id falls back to na]]
- [[Facebook page userID is the viewer not the profile owner]]
- [[--max-expand caps comment batches not profile count; profile-visit phase dominates runtime]]
- [[SameSite=None cookies require Secure or Chromium drops them]]

%% ai-graph-end %%