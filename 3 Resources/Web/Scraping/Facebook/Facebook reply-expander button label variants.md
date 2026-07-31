---
title: "Facebook reply-expander button label variants"
created: 2026-06-21
type: lesson
status: seedling
source: "session 2026-06-21"
tags: [facebook, scraping, gotcha, regex, fb-info-project]
---

# Facebook reply-expander button label variants

To load nested comment replies in a Facebook scrape you must click the reply-thread **expander button**, and Facebook labels it differently depending on how many replies the thread has. A regex that only matches `View N replies` / `Xem N phản hồi` silently misses the most common multi-reply forms, so those threads never expand and their commenters are never harvested.

**Label variants seen (EN / VI):**
- `View 3 replies` / `Xem 3 phản hồi`
- `View all 12 replies` / `Xem tất cả 12 phản hồi`  ← the standard label once a thread has several replies
- `View more replies` / `Xem thêm phản hồi`  ← nested expander revealed after the first click
- `View previous replies` / `Xem các phản hồi trước`
- bare count `12 replies` / `12 phản hồi`  ← the count itself is the expander

**Gotcha:** the matcher must require a `view`/`see`/`xem` verb (or be a bare `<number> replies`) so it does NOT also click the **compose** button (`Reply` / `Trả lời`).

**Second gotcha (loop robustness):** clicking one expander re-renders the thread and can reveal further `View more replies`, and freshly-snapshotted button locators go stale. So the expansion loop must scroll + re-query each round and use a *stale counter* (stop only after N consecutive empty rounds) rather than breaking on the first round that finds/clicks nothing.

In `fb-info-project` this is `patterns.REPLY` + collector pass 2.

## Related

- [[Facebook UID from a vanity handle via the mbasic lst token]]
