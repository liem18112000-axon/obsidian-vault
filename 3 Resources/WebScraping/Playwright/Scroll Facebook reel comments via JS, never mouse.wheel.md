---
title: "Scroll Facebook reel comments via JS, never mouse.wheel"
created: 2026-06-13
type: gotcha
status: seedling
source: "session 2026-06-13 fb-info-project reel scroll bug"
tags: [playwright, facebook, scraping, gotcha, reels]
---

# Scroll Facebook reel comments via JS, never mouse.wheel

Facebook reels advance the reel **carousel** (jump to next/previous reel) whenever a physical `page.mouse.wheel()` event reaches the video player — and this happens **even when the mouse pointer is parked over the right-side comment panel**, because the wheel event still bubbles to the video underneath.

So when scraping a reel with Playwright, to scroll its comment side panel you must **not** use `page.mouse.wheel()` or keyboard scroll keys (`End`, arrows) at all. Scroll the panel directly in the page via JS — `element.scrollTo(0, element.scrollHeight)` on the scrollable nested `div`. A physical wheel is only safe on normal posts/videos, where there's no carousel to trigger.

This was the root cause of a bug in `fb-info-project` `src/browser.py` `scroll(panel=True)`: `mouse.wheel(0, 15000)` fired unconditionally for every source, so reel scraping kept jumping to the next reel instead of loading the current reel's comments. Fix: gate the physical wheel + `End` behind `if not panel:`, and let the JS DOM-scroll handle reels.

**But avoiding `mouse.wheel` is not sufficient — the JS must scroll the RIGHT element.** A blunt JS that scrolls `document.documentElement`/`body`/`[role=feed]`/`[role=main]` plus "the N largest scrollable divs" *also* moves the reel/video pane (the video container is itself a large scrollable div), which scrolls the reel away from view. The precise rule: scroll **only the comment list**, found as the **nearest scrollable ancestor of each `div[role="article"]`** (a comment), and nothing else:

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

**Tell-tale for the jump:** the page's final reel URL/id changes (e.g. the loaded `/reel/<id>` differs from the one you navigated to) — proof the carousel advanced. After the fix the id stays put.

**Testing it for real:** build a synthetic DOM with a scrollable video pane (no articles) + a scrollable comment panel (holding `role=article`s) + a tall body, run the scroll, and assert only the comment panel's `scrollTop > 0` while the video pane and document stay at `0`.

## Related

- [[Playwright]]
