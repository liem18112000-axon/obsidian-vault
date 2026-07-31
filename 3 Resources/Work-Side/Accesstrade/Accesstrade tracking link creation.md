---
ai_hash: c351032ef2989f97
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
aliases:
- Accesstrade product_link create
- Accesstrade deep link
- Accesstrade affiliate link API
created: 2026-06-11
entities: []
source: research session 2026-06-11
status: seedling
tags:
- affiliate
- accesstrade
- api
- tracking-link
- howto
title: Accesstrade tracking link creation
type: howto
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

On the global **OBS** generation there is no `product_link/create`; the quicklink / custom-creative endpoints play this role — see [[Accesstrade Creative APIs]].

## Best practices

- **Batch** multiple `urls` in one call rather than one request per URL.
- Always set a meaningful `sub1` (e.g. the content slug) so revenue is traceable back to the exact post.
- Don't re-mint on every run — cache by content hash of the inputs: [[Idempotent link minting with content-hash cache keys]].
- Pass the **numeric** `campaign_id`, not the merchant slug — [[Accesstrade classic campaign campaign_id is the numeric id, raw.merchant is the slug]].

## Related

- [[Accesstrade SubID attribution]]
- [[Accesstrade Creative APIs]]
- [[Use case - bulk tracking link generation]]
- [[Accesstrade Campaigns API]]
- [[Accesstrade API Integration - MOC]]

%% ai-graph-start %%

**Related notes:**
- [[Accesstrade Creative APIs]]
- [[Use case - bulk tracking link generation]]
- [[Accesstrade SubID attribution]]
- [[Accesstrade Campaigns API]]
- [[Accesstrade classic campaign campaign_id is the numeric id, raw.merchant is the slug]]

%% ai-graph-end %%