---
title: Accesstrade tracking link creation
created: 2026-06-11
type: howto
status: seedling
source: research session 2026-06-11
tags:
  - affiliate
  - accesstrade
  - api
  - tracking-link
  - howto
aliases:
  - Accesstrade product_link create
  - Accesstrade deep link
  - Accesstrade affiliate link API
---

# Accesstrade tracking link creation

**The tracking-link endpoint converts an ordinary merchant/product URL into an attributed affiliate (deep) link that credits your account when a click converts.** This is the most-called write endpoint in any affiliate automation.

## Classic `.vn` — `POST /v1/product_link/create`

Request body:

| Field | Meaning |
|---|---|
| `campaign_id` *(required)* | campaign you're approved on |
| `urls[]` | one or more origin/landing URLs to convert |
| `utm_source`, `utm_medium`, `utm_campaign`, `utm_content` | passed through for your own analytics |
| `sub1`–`sub4` | your tracking tags — see [[Accesstrade SubID attribution]] |
| `url_enc` | whether origin URLs are URL-encoded |

Response: `data.success_link[]` with `aff_link` (full deep link) and `short_link` (shortened).

```bash
curl -X POST "https://api.accesstrade.vn/v1/product_link/create" \
  -H "Authorization: Token $AT_KEY" -H "Content-Type: application/json" \
  -d '{"campaign_id":"tikivn","urls":["https://tiki.vn/dp/123"],
       "utm_source":"blog","sub1":"review-post-42"}'
```

There is also a **TikTok Shop** variant: `POST /v2/tiktokshop_product_feeds/create_link` (`product_url`, `product_id`, `utm_*`, `sub_1-4`, `minify`).

## Global OBS — Creative quick link / custom creative

On OBS you get a ready link via the **Creative APIs** instead: `GET .../creatives/quicklink` returns an `affiliateLink`, and `POST .../creatives/custom` builds a branded link for a validated `landingUrl` with `subIds`. See [[Accesstrade Creative APIs]].

## Best practices

- **Batch** multiple `urls` in one call rather than one request per URL.
- Always set a meaningful `sub1` (e.g. the content slug) so revenue is traceable back to the exact post.
- Cache the `aff_link` keyed by `(campaign_id, origin_url, subs)` — the same inputs always yield the same attributed link, so don't re-mint on every run.

## Related

- [[Accesstrade SubID attribution]]
- [[Accesstrade Creative APIs]]
- [[Use case - bulk tracking link generation]]
- [[Accesstrade Campaigns API]]
- [[Accesstrade API Integration - MOC]]
