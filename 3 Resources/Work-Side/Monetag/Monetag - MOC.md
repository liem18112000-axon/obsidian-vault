---
ai_hash: 7a8f1a50e99f5029
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
aliases:
- Monetag index
- Monetag research
created: 2026-06-12
entities: []
source: research session 2026-06-12 (promo.monetag.com/vn + third-party reviews)
status: seedling
tags:
- affiliate
- monetag
- ad-network
- monetization
- moc
title: Monetag - MOC
type: moc
---

# Monetag - MOC

Index for the **Monetag** research cluster (`promo.monetag.com/vn`). What Monetag *is* → [[Monetag overview]].

> [!info] Source & freshness
> Figures (CPM, payout thresholds, publisher counts) come from the Vietnam landing page and third-party reviews captured **June 2026**, plus the official API/help docs. Verify current terms on `monetag.com` before relying on numbers.

```mermaid
flowchart TD
    P[Publisher: site / app / Telegram Mini App] -->|installs MultiTag / SDK| M[Monetag SSP]
    M -->|fills ad slots from| A[Global advertisers / PropellerAds demand]
    A -->|CPM / CPC| M
    M -->|weekly payout Thu| P
    P -->|refers others, +5%| R[Referral income]
```

## Notes

- [[Monetag overview]] — what it is, model, PropellerAds lineage, scale
- [[Monetag ad formats]] — popunder, push, in-page push, interstitial, vignette, smartlink, MultiTag
- [[Monetag payments and payout terms]] — methods, thresholds, weekly schedule
- [[Monetag referral program]] — 5% lifetime referral
- [[Monetag SSP API and integration]] — MultiTag tag, Zones, Statistics, API v5, Telegram SDK
- [[Ad network vs affiliate network]] — Monetag (CPM) vs Accesstrade (CPS): the key distinction

## Bridge to the Accesstrade work

- [[Accesstrade API Integration - MOC]] — the affiliate-network side; same Claude-automation patterns ([[Designing an Accesstrade skill for Claude Code|skill]] + [[Claude Code hooks event model|hooks]]) apply to Monetag's reporting API.

## Related

- [[Ad network vs affiliate network]]
- [[Monetag SSP API and integration]]

%% ai-graph-start %%

**Related notes:**
- [[Monetag overview]]
- [[Monetag SSP API and integration]]
- [[Ad network vs affiliate network]]
- [[Monetag ad formats]]
- [[Monetag referral program]]

%% ai-graph-end %%