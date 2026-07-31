---
title: "Facebook reel comments are hidden behind the comment icon"
created: 2026-06-06
type: lesson
status: seedling
source: "fb-info-project session 2026-06-06"
tags: [facebook, scraping, playwright, gotcha, fb-info-project]
---

# Facebook reel comments are hidden behind the comment icon

Facebook reel pages (`facebook.com/reel/<id>`) do **not** render their comment section on load — comments sit behind the comment icon, and only clicking it opens the comment side panel. Post pages, by contrast, have `div[role="article"]` comment containers present immediately — and **video pages** (`facebook.com/<page>/videos/<id>`) behave like posts, not reels: comments render on load, no icon click needed, so a video mode can reuse the post flow verbatim.

**Login is a hard requirement (confirmed 2026-06-06):** reel comments are login-walled — an anonymous/expired session gets the recurring [[Facebook shows a See more on Facebook login dialog when the session is logged out|"See more on Facebook" login dialog]] instead of the comment panel, and no amount of dismissing it makes the comments load. If reel comments never appear *and* the login popup was seen, the diagnosis is always "refresh the session", not "DOM changed".

Once the panel is open, comments use the exact same DOM as on posts (`div[role="article"]` containers, "View more comments" / "Xem thêm bình luận" expanders), so the post-scraping flow works unchanged.

**Gotchas:**
- The comment button is found by aria-label, which is locale-dependent (`Comment` EN / `Bình luận` VI) **and the label may carry a trailing count** — match by prefix, not equality: Playwright `get_by_label(re.compile(r"^(comment|bình luận)", re.I))`, or CSS contains `div[role="button"][aria-label*="omment" i]`.
- If no button is found, the panel may already be open — check for `div[role="article"]` first and treat the click as best-effort, not fatal.
- Scrolling the opened panel has its own trap — see [[Scroll Facebook reel comments via JS, never mouse.wheel]].

**Design decision (fb-info-project `scraper.py`):** reel mode = post mode + an `open_reel_comments()` pre-step before the shared `collect()` flow, rather than a separate reel collector. Keeps one comment-expansion/extraction code path.

## Related

- [[Facebook comment DOM uses role=article containers]]
