---
type: term
domain: affiliate-marketing
aliases: [Attribution Model, Attribution, Last-Click, First-Click, Multi-Touch Attribution]
tags: [affiliate, tracking]
---

# Attribution Model

> [!summary]
> An **Attribution Model** is the rule that decides **which affiliate gets credited** (and paid) for a conversion when a customer's journey involved more than one touchpoint. Because most buyers click several links, read several reviews, and return multiple times before acting, the model determines who wins the commission.

## Plain-English definition

A customer rarely converts from a single click. They might read your review, click your link, leave, watch a YouTube affiliate's video, click *their* link, then finally buy. Two affiliates touched that sale. **Attribution decides who gets paid** — and in affiliate marketing, usually only one does.

## The common models

| Model | Who gets the commission | Notes |
|-------|-------------------------|-------|
| **Last-click** | The **last** affiliate clicked before the action | The de-facto standard in affiliate marketing |
| **First-click** | The **first** affiliate to introduce the customer | Rewards discovery / top-of-funnel |
| **Multi-touch** | Commission **split** across touchpoints | Fairer but rare; complex to track |
| **Last non-direct** | Last affiliate, ignoring direct visits | Common in analytics, less in affiliate payouts |

> [!important] Last-click is the default — build around it
> The overwhelming majority of affiliate programs pay **last-click**. Practically, that means: **be the last link the buyer clicks before they convert.** Bottom-of-funnel content (reviews, "best X", comparison, coupon pages) wins under last-click because it sits closest to the purchase decision.

## How it interacts with cookies

Attribution and [[Cookie Duration]] work together:

- The cookie defines the **window** in which an action can be credited.
- The attribution model decides **whose** cookie wins if several are present.

Under last-click, a later affiliate's click **overwrites** your cookie — so even if you introduced the customer and you're still inside your window, a competitor clicked-into just before checkout takes the commission.

## Worked example

A customer's path to a $200 purchase:

1. Day 1 — reads **your** review, clicks your link (your cookie set).
2. Day 3 — watches a coupon site's video, clicks their link (cookie overwritten).
3. Day 3 — buys.

| Model | Who's paid |
|-------|-----------|
| Last-click | **Coupon site** (the last click) |
| First-click | **You** (the introducer) |
| Multi-touch | Split, e.g. 50/50 |

Same sale, completely different payout depending on the model. This is why coupon/deal sites thrive under last-click — they intercept the final click.

## Common gotchas

> [!warning] You can earn the click and still lose the sale
> Under last-click, doing the hard work of *introducing* the buyer earns nothing if someone else grabs the final click. Understand the program's model before you invest in top-of-funnel content you can't monetise.

- **Cross-device breaks attribution** — click on phone, buy on laptop, and the cookie/attribution chain may snap entirely (no one is credited, or the wrong party is).
- **Coupon/loyalty extensions** — browser extensions that inject their affiliate link at checkout hijack last-click attribution; some merchants now exclude them.
- **Direct-to-merchant** — if the buyer types the URL directly at the end, a "last non-direct" model still credits the prior affiliate; pure last-click may not.
- **Self-reported / coupon-code attribution** — some programs attribute by the *code used* rather than the click, bypassing cookies entirely.

## When attribution should shape your strategy

- **Last-click program** → focus on bottom-of-funnel, decision-stage content; be the final touch.
- **First-click program** (rare) → top-of-funnel discovery content pays off.
- **High-consideration niches** → expect long journeys with many touchpoints; last-click rewards whoever owns the "ready to buy" moment.

## Related terms

- [[Cookie Duration]] — sets the window; attribution decides whose cookie wins inside it.
- [[Cost per Action]] / [[Cost per Sale]] / [[Cost per Lead]] — the payouts attribution assigns.
- [[Earnings per Click]] — affected by how many of your introduced conversions you actually get credited for.
- [[Reversal]] — even a correctly-attributed sale can be reversed later.

---
*See also: [[3 Resources/Work-Side/Affiliate/Term|all affiliate terms]]*
