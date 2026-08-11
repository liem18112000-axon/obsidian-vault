---
ai_hash: d62e5650771af9ff
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
aliases:
- Accesstrade API best practices
- Caching backoff idempotency affiliate
created: 2026-06-11
entities: []
source: research session 2026-06-11
status: seedling
tags:
- affiliate
- accesstrade
- api
- best-practices
- moc
title: Affiliate API engineering best practices
type: moc
---

# Affiliate API engineering best practices

**Treat the Accesstrade API as rate-limited, eventually-consistent, and money-critical: cache reads, back off on limits, make writes idempotent, and reconcile against the report API as the source of truth.** Hub note — each practice is owned by the linked note.

| Practice | Owning note |
|---|---|
| Cache reads, pull incrementally, back off; page through 7-day windows ≥5 min apart | [[Accesstrade API rate limits and pagination]] |
| Idempotent writes — hash every output-affecting input into the cache key | [[Idempotent link minting with content-hash cache keys]] |
| Reconcile status flips — pull wider than you report, upsert by id | [[To catch status flips, re-pull a lookback window wider than the report window]] |
| Report API is the source of truth; postbacks are fast-but-lean signals to confirm later | [[Accesstrade conversion and transaction reporting]], [[Accesstrade postback and S2S conversion tracking]] |
| Disclosure, approved landing pages, link health | [[Affiliate compliance and link hygiene]] |
| Key in env/secret store, never in a note or repo | [[Secrets handling for affiliate API keys]] |

## Defensive details not owned elsewhere

- Always separate **approved** from **pending** in any total — pending is not earned.
- Before applying to a campaign, check current affiliation state to avoid duplicate/again-rejected applications.
- URL-encode date params; mind timezones (OBS dates carry TZ).
- Identify the [[Accesstrade has two API generations|API generation]] before copying any query — casing and paths differ.
- Log raw responses (minus secrets) so you can debug attribution disputes.

## Related

- [[Accesstrade API Integration - MOC]]

%% ai-graph-start %%

**Related notes:**
- [[Idempotent link minting with content-hash cache keys]]
- [[To catch status flips, re-pull a lookback window wider than the report window]]
- [[Accesstrade API rate limits and pagination]]
- [[Accesstrade API Integration - MOC]]
- [[Secrets handling for affiliate API keys]]

%% ai-graph-end %%