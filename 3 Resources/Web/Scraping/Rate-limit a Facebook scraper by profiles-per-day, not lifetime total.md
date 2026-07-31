---
ai_hash: e76b1b811ad0b5a9
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-14
entities: []
source: fb-info-project session 2026-06-14
status: seedling
tags:
- facebook
- scraping
- rate-limiting
- anti-block
title: Rate-limit a Facebook scraper by profiles-per-day, not lifetime total
type: lesson
---

# Rate-limit a Facebook scraper by profiles-per-day, not lifetime total

When rate-limiting an automated Facebook scraper, the safety-critical knob is **profiles viewed per day on one account**, not the lifetime total. Facebook checkpoints/blocks are triggered by **velocity** (burst of automated page loads in a short window), not cumulative volume.

**Design consequence:** keep a daily profile cap on *every* license tier — even an otherwise "unlimited" tier — because that cap protects the user's own FB account from being blocked. A blocked account is a worse user experience than hitting a quota message and waiting until tomorrow.

Rough sustainable range observed/reported: low hundreds to ~1–2k automated profile views/day on a normal account before block risk climbs sharply. Treat numbers as starting points and tune against real block rates.

Applied in [[Offline signed-token licensing for distributed binaries]] (the license tiers).

## Related

- [[Offline signed-token licensing for distributed binaries]]

%% ai-graph-start %%

**Related notes:**
- [[Row-per-event output breaks row-count quota accounting]]
- [[--max-expand caps comment batches not profile count; profile-visit phase dominates runtime]]
- [[scrapling goto waits for load event + retries=3; on FB SPA that means ~90s per dead profile]]

%% ai-graph-end %%