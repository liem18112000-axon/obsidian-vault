---
ai_hash: 9d115a05d5bb5cb0
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
aliases:
- Monetag formats
- MultiTag
- Monetag popunder push
created: 2026-06-12
entities: []
source: research session 2026-06-12
status: seedling
tags:
- monetag
- ad-network
- ad-formats
title: Monetag ad formats
type: term
---

# Monetag ad formats

**Monetag monetizes through a set of high-yield, mostly interruptive ad formats, and its flagship "MultiTag" bundles the top five into a single script that auto-optimizes which one to show.** Choosing formats is the main lever a publisher controls over revenue vs user experience.

## The formats

| Format | What it is | Note |
|---|---|---|
| **Popunder (OnClick)** | full-page ad opens behind/after a click | highest revenue, most intrusive |
| **Push notifications** | browser push messages after opt-in | recurring revenue from subscribers |
| **In-Page Push (IPP)** | push-style cards rendered *in* the page (no opt-in) | works on iOS/where native push can't |
| **Interstitial** | dismissible full-screen ad, high CPM | |
| **Vignette** | small banner with a close button | lighter touch |
| **Banner** | classic display banner | least intrusive, lower CPM |
| **Smartlink / Direct Link** | a single URL that routes to the best offer | for non-site traffic: 404s, expired domains, toolbars, social, messengers |
| **MultiTag** | one tag = OnClick + Push + IPP + Interstitial + Vignette, auto-mixed | the recommended default |

## Practical notes

- Reported **Vietnam CPM ~$3+** (landing-page claim, explicitly *not guaranteed*).
- **MultiTag** is the simplest integration: paste one script and Monetag decides the format mix per visit — see [[Monetag SSP API and integration]].
- **Smartlink** is what makes Monetag usable for affiliate-style/arbitrage traffic that has no website to host banners.

## Related

- [[Monetag SSP API and integration]]
- [[Monetag overview]]
- [[Monetag - MOC]]

%% ai-graph-start %%

**Related notes:**
- [[Monetag - MOC]]
- [[Monetag overview]]
- [[Monetag SSP API and integration]]
- [[Ad network vs affiliate network]]
- [[Monetag referral program]]

%% ai-graph-end %%