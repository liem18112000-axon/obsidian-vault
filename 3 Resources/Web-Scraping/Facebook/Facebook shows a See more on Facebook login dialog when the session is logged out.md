---
title: "Facebook shows a See more on Facebook login dialog when the session is logged out"
created: 2026-06-06
type: lesson
status: seedling
source: "fb-info-project session 2026-06-06"
tags: [facebook, scraping, playwright, gotcha, fb-info-project]
---

# Facebook shows a See more on Facebook login dialog when the session is logged out

When a Facebook scraping session is logged out or its saved cookies have expired, content pages (posts, reels) load with a modal **"See more on Facebook"** login dialog (`div[role="dialog"]`) layered over the content. It blocks all clicks/scrolls underneath, so automation must dismiss it before interacting.

**How to dismiss:**
- Find the dialog by its text (`see more on facebook` / `log in` / `đăng nhập`) — do not match any bare `role="dialog"`, other panels can use that role.
- Click its X button: aria-label `Close` (EN) / `Đóng` (VI); fall back to `Escape`.
- Be careful with fallback selectors inside the dialog — a loose `[aria-label]` match can click the email input instead of the X; restrict to `[role="button"][aria-label*="lose" i]`.

**Timing gotcha:** the dialog can appear a few seconds *after* page load — and when logged out it **re-appears again and again while scrolling**. One-shot dismissal is not enough: check-and-close at the top of *every* scroll/expand iteration (the existence check is one cheap `count()` query), and log the "session expired" warning only once (e.g. a function attribute flag) so it doesn't spam.

**Signal value:** its presence means the saved session is dead — log a warning suggesting re-login (`python scraper.py setup` in fb-info-project), because results will be limited even after dismissal.

## Related

- [[Facebook reel comments are hidden behind the comment icon]]
- [[Scrolling a Facebook reel page must target the comment panel, not the video]]
