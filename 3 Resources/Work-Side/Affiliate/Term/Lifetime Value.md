---
ai_hash: c1146ddc317550cc
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
aliases:
- LTV
- CLV
- Customer Lifetime Value
- Lifetime Value
domain: affiliate-marketing
entities: []
tags:
- affiliate
- metric
type: term
---

# Lifetime Value

> [!summary]
> **Lifetime Value (LTV)** — also written **CLV** (Customer Lifetime Value) — is the total revenue a customer generates over the entire time they stay with a merchant, not just on their first purchase. In affiliate marketing it is the number that decides whether a recurring [[Revenue Share]] deal beats a one-off [[Cost per Sale]] bounty.

## Plain-English definition

A customer is rarely worth just their first order. They renew, re-buy, upgrade, and stay subscribed for months or years. **LTV** adds all of that up: *how much money this customer is worth, start to finish.*

For an affiliate this matters because it tells you the true ceiling on what a referral is worth — and therefore which payout model captures more of it.

## The formula

A simple, common form for subscription products:

```
LTV = average revenue per period × average customer lifetime (in periods)
```

Example: a customer paying $30/month who stays an average of 20 months →
`$30 × 20 = $600` LTV.

A retention-based form (when you know the churn rate):

```
LTV = average revenue per period ÷ churn rate
```

A $30/month product with 5% monthly churn → `$30 ÷ 0.05 = $600` LTV. (Churn rate and average lifetime are two views of the same thing: `average lifetime ≈ 1 ÷ churn rate`.)

## Why LTV decides your payout model

Your affiliate earnings are a *slice* of the customer's LTV. Which model gives you the bigger slice depends on how long customers stay:

> [!tip] LTV is the tie-breaker between RevShare and a one-off bounty
> Customer pays $30/mo, you're offered either a **$50 one-off [[Cost per Sale]]** or **25% [[Revenue Share]]**:
> - Customer stays 3 months (LTV $90) → RevShare = $22.50 → **bounty wins**
> - Customer stays 12 months (LTV $360) → RevShare = $90 → **RevShare wins**
> - Customer stays 3 years (LTV $1,080) → RevShare = $270 → **RevShare crushes it**
>
> High-LTV (low-churn, sticky) products favour [[Revenue Share]]; low-LTV (high-churn) products favour grabbing the flat [[Cost per Sale]] bounty now.

## LTV across the funnel metrics

LTV is the long-horizon cousin of the per-conversion metrics:

- [[Average Order Value]] = value of **one order**.
- LTV = value of **all orders** a customer ever makes.
- [[Earnings per Click]] for recurring offers should be modelled over **LTV**, not a single payout — otherwise you undervalue [[Revenue Share]] offers.

## What raises LTV

- **Low churn / high retention** — the single biggest lever; customers staying longer.
- **Upsells & tier upgrades** — customers moving to pricier plans over time.
- **Cross-sells & repeat purchase** — buying more products.
- **Price increases** the customer accepts without leaving.

As an affiliate you don't control these — but you benefit from choosing **merchants with high LTV** (sticky SaaS, essential tools, low-churn services) when promoting recurring deals.

## Common gotchas

> [!warning] LTV is an estimate, and merchant-dependent
> - It's a **projection** built on *average* lifetime/churn — any single customer can churn next month. Don't treat a high LTV as guaranteed income.
> - You're exposed to the **merchant's** retention, pricing, and survival. A product that degrades, hikes prices, or shuts down collapses the LTV your [[Revenue Share]] depends on.
> - **Gross revenue LTV ≠ profit LTV.** Merchants often quote LTV net of costs; the revenue figure your commission is based on may differ.
> - **Early churn + [[Reversal]]** can claw back the first cycles, so realised LTV-based earnings start later than they appear.

## When LTV should drive your decision

- Choosing between a **[[Revenue Share]] vs one-off bounty** on the same product — model both against the product's typical customer lifetime.
- Picking **which recurring merchant** to promote — prefer high-LTV, low-churn products.
- Deciding **paid-traffic budgets** — your max profitable cost-per-acquisition is bounded by the LTV slice you'll earn, not the first payment.

## Related terms

- [[Revenue Share]] — the model whose value is governed by LTV; this note is its key supporting concept.
- [[Cost per Sale]] — the one-off alternative LTV is weighed against.
- [[Average Order Value]] — single-order value; LTV is the all-orders, full-lifetime view.
- [[Earnings per Click]] — for recurring offers, compute EPC over LTV, not one payout.
- [[Reversal]] — early churn can reverse recent cycles, lowering realised LTV earnings.

---
*See also: [[3 Resources/Work-Side/Affiliate/Term|all affiliate terms]]*

%% ai-graph-start %%

**Related notes:**
- [[Revenue Share]]
- [[Term]]
- [[Average Order Value]]
- [[Cost per Sale]]
- [[Cookie Duration]]

%% ai-graph-end %%