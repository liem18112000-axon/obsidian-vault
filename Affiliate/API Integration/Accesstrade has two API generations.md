---
title: Accesstrade has two API generations
created: 2026-06-11
type: concept
status: seedling
source: research session 2026-06-11
tags:
  - affiliate
  - accesstrade
  - api
  - gotcha
aliases:
  - Accesstrade classic vs OBS API
  - Accesstrade API versions
---

# Accesstrade has two API generations

**Accesstrade exposes two distinct, non-interchangeable API surfaces, and confusing them is the #1 integration trap.** They use different hosts, different path shapes, and different identifiers — but the *same* `Token` auth header.

| | Classic Publisher API | Global "OBS" API |
|---|---|---|
| Docs | `developers.accesstrade.vn` | `support.accesstrade.global` |
| Base host | `https://api.accesstrade.vn` | OBS gateway (region host) |
| Path style | flat: `/v1/campaigns`, `/v1/transactions` | resource-nested: `/v1/publishers/me/sites/{siteId}/...` |
| Key id | `campaign_id`, `merchant` | `siteId` + `campaignId` |
| Params | `snake_case` (`since`, `sub1`) | `camelCase` (`fromDate`, `subIds`) |
| Flavor | VN-centric, long-standing | newer, multi-country, more RESTful |

## Why two exist

The classic `.vn` API grew with the Vietnam market and is what most VN publisher integrations (and tools like wecantrack) target. The OBS API is the newer, more uniform platform rolled out for the global/multi-country footprint. Both are live; which one your credentials hit depends on your account/region.

## The practical rule

Pick **one** generation per integration and don't mix paths. When you read a code sample or doc snippet, first identify the generation by its **path shape** (`/publishers/me/sites/{siteId}/` ⇒ OBS; flat `/v1/<thing>` ⇒ classic). A `siteId` requirement is the fastest tell.

> [!warning]
> Parameter casing differs (`snake_case` vs `camelCase`). Copy-pasting a query string from the wrong generation silently returns empty results, not an error.

## Related

- [[Accesstrade Publisher API authentication]]
- [[Accesstrade Campaigns API]]
- [[Accesstrade API rate limits and pagination]]
- [[Accesstrade API Integration - MOC]]
