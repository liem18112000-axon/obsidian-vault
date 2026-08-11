---
ai_hash: dfbcb0f3858e25c7
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-21
entities: []
source: session 2026-06-21
status: seedling
tags:
- facebook
- scraping
- gotcha
- uid
- fb-info-project
title: Facebook page userID is the viewer not the profile owner
type: lesson
---

# Facebook page userID is the viewer not the profile owner

When scraping a Facebook profile page while logged in, the `"userID":"<digits>"` field embedded in the page's bootstrap JavaScript is the **logged-in viewer's** id — NOT the profile owner you are looking at. It is identical on every page you load. A UID extractor that matches `"userID"` therefore stamps the **same wrong id (the scraper account's own) onto every harvested profile**.

**Symptom that pins it instantly:** every output row has an identical UID across clearly different profile URLs.

**Reliable owner id instead:** the app-link meta tags in the page `<head>` —
`<meta property="al:android:url" content="fb://profile/<ownerId>?...">` and the `al:ios:url` twin — describe the page's *subject*, i.e. the profile owner. Match `fb://profile/(\d+)`. Other JSON fields (`entity_id`, `profile_id`, `identifier`) are ambiguous on a real page (the leftmost regex match may be a chrome element or a friend card), so prefer the app-link meta and, failing that, the mbasic `lst=<viewer>:<owner>:<ts>` token.

**Second-order bug it caused in fb-info-project:** because the wrong viewer id was non-empty, it pre-empted the (correct) mbasic fallback, which only runs when the UID is empty. Fix = stop trusting `userID` so the page yields the owner via `fb://profile` or falls through to mbasic.

Related: [[Facebook UID from a vanity handle via the mbasic lst token]].

## Related

- [[Facebook UID from a vanity handle via the mbasic lst token]]

%% ai-graph-start %%

**Related notes:**
- [[Facebook UID from a vanity handle via the mbasic lst token]]
- [[Facebook Comet comment DOM does not expose the commenter's numeric UID]]
- [[FB photofbid= links scrape as post mode; filename id falls back to na]]
- [[Stale FB session signature login popup + profile 302 to login + empty location columns]]
- [[Unanchored 'From' regex captures the profile name from Facebook's 'See more from' buttons]]

%% ai-graph-end %%