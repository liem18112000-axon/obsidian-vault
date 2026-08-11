---
ai_hash: 6b9a86388ae37c87
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
aliases:
- Accesstrade creatives
- Accesstrade quicklink
- Accesstrade custom creative
created: 2026-06-11
entities: []
source: research session 2026-06-11
status: seedling
tags:
- affiliate
- accesstrade
- api
- creatives
- obs
title: Accesstrade Creative APIs
type: term
---

# Accesstrade Creative APIs

**On the global OBS API, "creatives" are the merchant-approved promotional assets — banners, text snippets, and ready-made affiliate links — that you embed in content; they are the OBS equivalent of the classic API's link-creation flow.** They keep you compliant by only emitting links to merchant-approved landing pages.

## Endpoints (OBS, all under `/v1/publishers/me/sites/{siteId}/campaigns/{campaignId}/creatives/`)

| Endpoint | Returns |
|---|---|
| `GET .../image` | banner creatives: image URL, dimensions, affiliate image link |
| `GET .../text` | text creatives: copy with embedded tracking link |
| `GET .../quicklink` | a single ready `affiliateLink` for the campaign |
| `GET .../custom/acceptedurls` | the `landingUrls` you're allowed to deep-link, with validation regex |
| `POST .../custom` | build a branded link: `landingUrl` *(required)*, `imageUrl`, `anchorText`, `name`, `subIds` |

## The key safety rail: accepted URLs

`POST .../custom` will only mint a link for a `landingUrl` that matches the merchant's **accepted-URL regex** (from `.../custom/acceptedurls`). This prevents you from deep-linking to pages the advertiser hasn't authorized — a frequent cause of rejected conversions.

```mermaid
flowchart TD
    A[Want to link a deep page] --> B[GET custom/acceptedurls]
    B --> C{URL matches<br/>accepted regex?}
    C -- yes --> D[POST creatives/custom<br/>+ subIds]
    C -- no --> E[Use quicklink or<br/>an allowed landing page]
    D --> F[affiliateLink returned]
```

## Classic vs OBS

On the classic `.vn` API you mint links directly with [[Accesstrade tracking link creation|product_link/create]]. On OBS, the **quicklink / custom creative** endpoints play that role, with the accepted-URL guardrail baked in.

## Related

- [[Accesstrade tracking link creation]]
- [[Accesstrade has two API generations]]
- [[Affiliate compliance and link hygiene]]
- [[Accesstrade API Integration - MOC]]

%% ai-graph-start %%

**Related notes:**
- [[Accesstrade tracking link creation]]
- [[Accesstrade API Integration - MOC]]
- [[Accesstrade has two API generations]]
- [[Accesstrade Campaigns API]]
- [[Affiliate compliance and link hygiene]]

%% ai-graph-end %%