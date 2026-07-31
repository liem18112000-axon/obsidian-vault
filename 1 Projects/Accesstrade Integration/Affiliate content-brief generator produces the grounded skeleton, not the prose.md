---
ai_hash: 2183d866ed5227eb
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-12
entities:
- Affiliate content-brief generator
- Grounded skeleton
- Prose
- LLM
- Human writing layer
- Code pipeline
- Campaign rewards
- Best-sellers
- Prices/discounts
- Live coupons
- Tracking-link plan
- Marketing copy
- Deterministic code
- Facts
- Raw JSON
- use_cases/campaign_discovery
- Accesstrade
- ContentBrief dataclass
- Markdown
- Product comparison table
- Coupon CTA
- Sub1 link plan
- Writer checklist
- API data
- _suggest_angle helper
- Factual seed line
- Article
- Trust boundary
- API call
- Creative/judgment work
- Pipeline
- Mocked responses
- Use case - campaign discovery and datafeed content briefs
- Campaigns
- Reward percentage
- Demand
- Top products
- Datafeeds
- Offers
- Brief
- .vn endpoints
- Ranking signal precedence
- EPC
- Accesstrade SubID attribution
- Merchant top_products sales
- Datafeed discount
- Wrap a two-generation API as one shared transport plus one client per generation
- Affiliate content
- ContentBrief builder
source: session 2026-06-12, accesstrade_integration use_cases
status: seedling
tags:
- affiliate
- accesstrade
- content
- automation
- llm
- design-decision
title: Affiliate content-brief generator produces the grounded skeleton, not the prose
type: lesson
---

# Affiliate content-brief generator produces the grounded skeleton, not the prose

**When automating affiliate content with an LLM, the code pipeline should produce the data-grounded *skeleton* of the brief — real campaign rewards, real best-sellers, real prices/discounts, live coupons, and a tracking-link plan — and leave the prose to the LLM/human writing layer.** The deterministic code must never invent marketing copy; its value is gathering and joining the facts that raw JSON can'\''t synthesize on its own.

This was the key design choice for `use_cases/campaign_discovery` (Accesstrade): the builder emits a `ContentBrief` dataclass rendered to Markdown (product comparison table, coupon CTA, sub1 link plan, writer checklist), grounded entirely in API data. The `_suggest_angle` helper outputs a single factual seed line, not an article.

Why: it keeps the trust boundary clean — every number in the brief traces to an API call and is verifiable, while the creative/judgment work stays with the layer suited to it. It also makes the pipeline unit-testable with mocked responses (no LLM call in the loop).

Implements the flow in [[Use case - campaign discovery and datafeed content briefs]]: discover campaigns (reward%) → rank demand (top_products) → enrich (datafeeds) → coupon hook (offers) → assemble brief. All four are classic `.vn` endpoints. Ranking signal precedence: your own EPC (earnings grouped by [[Accesstrade SubID attribution|sub1]]) → merchant top_products sales → datafeed discount.

## Related

- [[Use case - campaign discovery and datafeed content briefs]]
- [[Accesstrade SubID attribution]]
- [[Wrap a two-generation API as one shared transport plus one client per generation]]

%% ai-graph-start %%

**Related notes:**
- [[Use case - campaign discovery and datafeed content briefs]]
- [[Use case - bulk tracking link generation]]
- [[Accesstrade classic campaign campaign_id is the numeric id, raw.merchant is the slug]]
- [[Accesstrade API Integration - MOC]]
- [[Accesstrade Datafeeds API]]

**Relations:**
- Affiliate content-brief generator — *produces* — Grounded skeleton
- Affiliate content-brief generator — *does not produce* — Prose
- Code pipeline — *produces* — Grounded skeleton
- Code pipeline — *leaves* — Prose
- Prose — *to* — LLM
- Prose — *to* — Human writing layer
- Grounded skeleton — *includes* — Campaign rewards
- Grounded skeleton — *includes* — Best-sellers
- Grounded skeleton — *includes* — Prices/discounts
- Grounded skeleton — *includes* — Live coupons
- Grounded skeleton — *includes* — Tracking-link plan
- Deterministic code — *must not invent* — Marketing copy
- Deterministic code — *gathers* — Facts
- Deterministic code — *joins* — Facts
- Raw JSON — *cannot synthesize* — Facts
- use_cases/campaign_discovery — *is a* — key design choice
- use_cases/campaign_discovery — *is associated with* — Accesstrade
- ContentBrief builder — *emits* — ContentBrief dataclass
- ContentBrief dataclass — *rendered to* — Markdown
- Markdown — *includes* — Product comparison table
- Markdown — *includes* — Coupon CTA
- Markdown — *includes* — Sub1 link plan
- Markdown — *includes* — Writer checklist
- ContentBrief dataclass — *grounded in* — API data
- _suggest_angle helper — *outputs* — Factual seed line
- _suggest_angle helper — *does not output* — Article
- Brief — *is* — verifiable
- Every number in the brief — *traces to* — API call
- Pipeline — *is* — unit-testable
- Pipeline — *uses* — Mocked responses
- Pipeline — *has* — no LLM call in the loop
- Flow — *implements* — Use case - campaign discovery and datafeed content briefs
- Flow — *includes step* — discover campaigns
- Flow — *includes step* — rank demand
- Flow — *includes step* — enrich
- Flow — *includes step* — coupon hook
- Flow — *includes step* — assemble brief
- discover campaigns — *involves* — Reward percentage
- rank demand — *involves* — Top products
- enrich — *involves* — Datafeeds
- coupon hook — *involves* — Offers
- discover campaigns — *is a* — .vn endpoints
- rank demand — *is a* — .vn endpoints
- enrich — *is a* — .vn endpoints
- coupon hook — *is a* — .vn endpoints
- Ranking signal precedence — *includes* — EPC
- Ranking signal precedence — *includes* — Merchant top_products sales
- Ranking signal precedence — *includes* — Datafeed discount
- EPC — *grouped by* — Accesstrade SubID attribution
- Use case - campaign discovery and datafeed content briefs — *is related to* — Affiliate content-brief generator
- Accesstrade SubID attribution — *is related to* — Affiliate content-brief generator
- Wrap a two-generation API as one shared transport plus one client per generation — *is related to* — Affiliate content-brief generator
- LLM — *automates* — Affiliate content
- Creative/judgment work — *stays with* — LLM
- Creative/judgment work — *stays with* — Human writing layer
- Trust boundary — *is kept* — clean
- EPC — *is a* — ranking signal
- Merchant top_products sales — *is a* — ranking signal
- Datafeed discount — *is a* — ranking signal
- Accesstrade — *has* — Campaigns
- Accesstrade — *has* — Top products
- Accesstrade — *has* — Offers
- Code pipeline — *value is* — gathering Facts
- Code pipeline — *value is* — joining Facts
- Affiliate content — *is automated with* — Code pipeline

%% ai-graph-end %%