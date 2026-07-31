---
ai_hash: 83a9e7c9db7386ab
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-14
entities: []
source: Accesstrade integration, session 2026-06-14
status: seedling
tags:
- accesstrade
- affiliate
- api
- gotcha
- identifiers
title: 'Accesstrade classic campaign: campaign_id is the numeric id, raw.merchant
  is the slug'
type: lesson
---

# Accesstrade classic campaign: campaign_id is the numeric id, raw.merchant is the slug

In the Accesstrade **classic** (.vn) API, a campaign carries two distinct identifiers and they are easy to confuse:

- **`campaign_id`** (extracted from the API row keys `campaign_id`/`id`) is the **numeric** id, e.g. `6988348030482561628`. This is what the **`product_link/create` minting** endpoint requires — it rejects the slug.
- **`raw["merchant"]`** is the human **slug**, e.g. `momo_ldp`, `tikivn`. This is what the **datafeed / brief** endpoints want.

So in the toolkit:
- Link Studio (minting) → use `campaign_id` (numeric).
- Content brief / datafeed reader → use the merchant **slug**; pass the numeric `campaign_id` separately as `mint_campaign_id` so the generated link plan is actually mintable.

The cached `campaigns` table stores `campaign_id` (numeric) as the key and keeps the slug only inside the JSON `raw.merchant`. A campaign can also have no product datafeed at all (e.g. a landing-page campaign like `momo_ldp` returns 0 products), so a brief built from it is empty but does not error.

## Which campaigns actually have a product datafeed

`raw.type` hints at the campaign kind: `type=3` is **CPS** (cost-per-sale, e-commerce) and these carry a reward % and *usually* have a product datafeed; `type=1`/`2` are landing-page / app-offer campaigns with **no** datafeed. But **reward % is NOT a reliable proxy for datafeed presence** — observed: `tiktok_cps` (type 3, 20% reward, highest) returns **0** products, while `tikivn` and `concung` return 8. So to default a UI to "a campaign that has products" you must actually **probe the datafeed** (a cheap 1-row/1-page peek per candidate), not just sort by reward. Probe highest-reward-first and cap the number of probes to bound latency.

Related: [[Mounting host gcloud ADC into a container to authenticate Vertex AI]].

%% ai-graph-start %%

**Related notes:**
- [[Accesstrade Campaigns API]]
- [[Affiliate content-brief generator produces the grounded skeleton, not the prose]]
- [[Accesstrade tracking link creation]]
- [[Use case - campaign discovery and datafeed content briefs]]
- [[Accesstrade Datafeeds API]]

%% ai-graph-end %%