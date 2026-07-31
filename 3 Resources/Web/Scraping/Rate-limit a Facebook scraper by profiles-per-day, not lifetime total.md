---
title: "Rate-limit a Facebook scraper by profiles-per-day, not lifetime total"
created: 2026-06-14
type: lesson
status: seedling
source: "fb-info-project session 2026-06-14"
tags: [facebook, scraping, rate-limiting, anti-block]
---

# Rate-limit a Facebook scraper by profiles-per-day, not lifetime total

When rate-limiting an automated Facebook scraper, the safety-critical knob is **profiles viewed per day on one account**, not the lifetime total. Facebook checkpoints/blocks are triggered by **velocity** (burst of automated page loads in a short window), not cumulative volume.

**Design consequence:** keep a daily profile cap on *every* license tier — even an otherwise "unlimited" tier — because that cap protects the user's own FB account from being blocked. A blocked account is a worse user experience than hitting a quota message and waiting until tomorrow.

Rough sustainable range observed/reported: low hundreds to ~1–2k automated profile views/day on a normal account before block risk climbs sharply. Treat numbers as starting points and tune against real block rates.

Applied in [[Offline signed-token licensing for distributed binaries]] (the license tiers).

## Related

- [[Offline signed-token licensing for distributed binaries]]
