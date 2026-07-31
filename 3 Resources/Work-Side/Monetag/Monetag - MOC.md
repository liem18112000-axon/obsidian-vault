---
title: Monetag - MOC
created: 2026-06-12
type: moc
status: seedling
source: research session 2026-06-12 (promo.monetag.com/vn + third-party reviews)
tags:
  - affiliate
  - monetag
  - ad-network
  - monetization
  - moc
aliases:
  - Monetag index
  - Monetag research
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
