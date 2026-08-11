---
ai_hash: 039ca814b52033ef
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-06
entities: []
source: fb-info-project session 2026-06-06
status: seedling
tags:
- facebook
- scraping
- playwright
- gotcha
- fb-info-project
title: Facebook shows a See more on Facebook login dialog when the session is logged
  out
type: lesson
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
- [[Scroll Facebook reel comments via JS, never mouse.wheel]]

%% ai-graph-start %%

**Related notes:**
- [[Facebook reel comments are hidden behind the comment icon]]
- [[Stale FB session signature login popup + profile 302 to login + empty location columns]]
- [[Distinguish absent control from missed click when expanding lazy lists]]
- [[Scroll Facebook reel comments via JS, never mouse.wheel]]
- [[Facebook post permalinks render the post twice — dialog plus a hidden page copy]]

%% ai-graph-end %%