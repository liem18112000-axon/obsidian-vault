---
title: "Scrolling a Facebook reel page must target the comment panel, not the video"
created: 2026-06-06
type: lesson
status: seedling
source: "fb-info-project session 2026-06-06"
tags: [facebook, scraping, playwright, gotcha, fb-info-project]
---

# Scrolling a Facebook reel page must target the comment panel, not the video

On a Facebook reel page, `mouse.wheel` over the **video** does not scroll comments — it advances to the **next reel**, silently abandoning the content being scraped. The comment side panel on the right is its own nested scrollable element, separate from the document.

To load more comments on a reel:
- Move the mouse over the panel first (e.g. `x = 0.85 * viewport width`) so wheel events land inside it.
- Avoid `keyboard.press("End")` — it acts on the document/video, not the panel, and can also trigger reel navigation.
- Belt-and-braces: in JS, find the largest nested scrollables (`scrollHeight > clientHeight + 80`) and `scrollTo(0, scrollHeight)` on them directly — works regardless of mouse position.

Implemented in fb-info-project `scraper.py` as `scroll(page, rounds, panel=...)` — one shared scroller with a `panel` flag instead of a separate reel scroller.

## Related

- [[Facebook reel comments are hidden behind the comment icon]]
