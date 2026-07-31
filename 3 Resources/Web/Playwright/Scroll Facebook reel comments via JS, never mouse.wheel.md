---
ai_hash: a9b8a849f0e2f832
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
aliases:
- Scrolling a Facebook reel page must target the comment panel, not the video
created: 2026-06-13
entities: []
source: fb-info-project sessions 2026-06-06 / 2026-06-13 (reel scroll bug)
status: seedling
tags:
- playwright
- facebook
- scraping
- gotcha
- reels
- fb-info-project
title: Scroll Facebook reel comments via JS, never mouse.wheel
type: gotcha
---

# Scroll Facebook reel comments via JS, never mouse.wheel

A reel's comments live in a **nested scrollable side panel**, not the document. A physical `page.mouse.wheel()` reaching the video player advances the reel **carousel** (next/previous reel), silently abandoning the content being scraped — and it does so **even with the pointer parked over the comment panel**, because the wheel event still bubbles to the video underneath. Same for keyboard `End`/arrows: they act on the document/video.

So on a reel, never use `mouse.wheel` or scroll keys — scroll the panel from inside the page with JS. (A physical wheel is fine on normal posts/videos, which have no carousel.)

**And the JS must scroll the RIGHT element.** Blunt JS that scrolls `documentElement`/`body`/`[role=feed]`/`[role=main]` plus "the N largest scrollable divs" also moves the video pane (itself a large scrollable div), scrolling the reel out of view. Precise rule: scroll only the comment list, found as the **nearest scrollable ancestor of each `div[role="article"]`**:

```js
const scrollable = e => e && e !== document.body && e !== document.documentElement
  && /(auto|scroll)/.test(getComputedStyle(e).overflowY)
  && e.scrollHeight > e.clientHeight + 40;
const scrollers = new Set();
document.querySelectorAll('div[role="article"]').forEach(a => {
  for (let e = a; e && e !== document.body; e = e.parentElement)
    if (scrollable(e)) { scrollers.add(e); break; }   // nearest scrollable ancestor only
});
scrollers.forEach(e => e.scrollTo(0, e.scrollHeight));
```

**Tell-tale for the jump:** the loaded `/reel/<id>` differs from the one you navigated to — proof the carousel advanced. After the fix the id stays put.

**Test it for real:** synthetic DOM with a scrollable video pane (no articles) + a scrollable comment panel (holding `role=article`s) + a tall body; assert only the comment panel's `scrollTop > 0` while the video pane and document stay at `0`.

Implemented in fb-info-project `src/browser.py` as one shared `scroll(page, rounds, panel=...)` — the physical wheel + `End` gated behind `if not panel:`, JS DOM-scroll for reels.

## Related

- [[Facebook reel comments are hidden behind the comment icon]]
- [[3 Resources/Web/Scraping/Facebook/Facebook sharev links can resolve to reels — classify after the redirect]]

%% ai-graph-start %%

**Related notes:**
- [[Facebook reel comments are hidden behind the comment icon]]
- [[Facebook sharev links can resolve to reels — classify after the redirect]]
- [[Switch Facebook comment sort to All comments before any scrolling or expansion]]
- [[Facebook shows a See more on Facebook login dialog when the session is logged out]]
- [[Verify Facebook comment sort switch by re-reading the sort button label]]

%% ai-graph-end %%