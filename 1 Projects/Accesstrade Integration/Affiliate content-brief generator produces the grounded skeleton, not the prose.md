---
title: "Affiliate content-brief generator produces the grounded skeleton, not the prose"
created: 2026-06-12
type: lesson
status: seedling
source: "session 2026-06-12, accesstrade_integration use_cases"
tags: [affiliate, accesstrade, content, automation, llm, design-decision]
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
