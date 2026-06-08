---
type: term
domain: affiliate-marketing
aliases: [Reversal, Clawback, Chargeback, Scrubbing, Reversed Commission]
tags: [affiliate, payout]
---

# Reversal

> [!summary]
> A **Reversal** (a.k.a. clawback) is when a commission that was provisionally credited to an affiliate is **cancelled and removed** because the underlying action turned out to be invalid — a refunded sale, a cancelled order, a rejected lead, fraud, or a duplicate. Reversals are why your *confirmed* earnings are always lower than your *pending* earnings.

## Plain-English definition

When a referred action happens, the commission isn't paid instantly — it sits in a **pending** state during a validation/hold period. If the action later fails to stick (the customer returns the product, the lead is junk, the card is charged back), the network **reverses** it: the pending commission disappears. If it was already paid, it's clawed back from your next payout.

## Why reversals exist

The advertiser only profits from *real, retained* actions. Paying for a sale that gets refunded, or a lead that's fake, would mean paying for nothing. The reversal mechanism protects the merchant and keeps the model honest — affiliates are paid for outcomes that actually held.

## What triggers a reversal

| Trigger | Model most affected |
|---------|---------------------|
| Product return / refund | [[Cost per Sale]] |
| Order cancellation before fulfilment | [[Cost per Sale]] |
| Chargeback (disputed card payment) | [[Cost per Sale]] |
| Lead fails validation (fake, duplicate, out-of-criteria) | [[Cost per Lead]] / [[Cost per Quality Lead]] |
| Fraudulent / bot traffic | All |
| Duplicate conversion | All |
| Buyer used a disallowed coupon / promo | [[Cost per Sale]] |

## The lifecycle of a commission

```
Action happens
   → PENDING        (hold / validation period — return window, lead review)
       ├─ holds up  → APPROVED → PAID
       └─ fails      → REVERSED (clawed back)
```

> [!tip] Track confirmed, not pending
> Your dashboard's pending balance is optimistic — some of it will reverse. Base [[Earnings per Click]], ROI, and ad-spend decisions on **confirmed** earnings after the typical reversal rate, never on pending.

## "Scrubbing" — the lead-gen cousin

In lead-gen ([[Cost per Lead]] / [[Cost per Quality Lead]]), removing invalid leads before payout is called **scrubbing**. A high "scrub rate" means the merchant rejects a large share of submitted leads.

> [!warning] Opaque scrubbing can hide underpayment
> Because the merchant judges lead validity, an excessive or unexplained scrub rate can be a backdoor to underpay affiliates. Ask for the historical reversal/scrub rate up front, prefer **objective, automated** validation over subjective manual review, and watch your approved-vs-submitted ratio over time.

## Worked example

A [[Cost per Sale]] offer pays 10%. In a month you drive:

- $5,000 in tracked sales → **$500 pending**.
- 15% of orders are returned within the window → $750 of sales reversed → **−$75**.
- **Confirmed earnings: $425.**

If you'd budgeted ad spend against the $500 pending, you'd have over-spent.

## How to manage reversals

- **Send quality traffic** — buyers with genuine intent return less; pre-qualified leads scrub less.
- **Know the reversal rate per offer** — factor it into [[Earnings per Click]] before scaling.
- **Match niche to return behaviour** — fashion/apparel has high return rates; digital goods and services have low ones.
- **Read the hold period** — long return windows (e.g. 60-day returns) delay confirmation and raise reversal exposure.

## Related terms

- [[Cost per Sale]] — refunds/chargebacks are the classic reversal trigger.
- [[Cost per Lead]] / [[Cost per Quality Lead]] — leads are reversed via scrubbing.
- [[Earnings per Click]] — must be computed on confirmed (post-reversal) earnings.
- [[Cookie Duration]] — a credited in-window sale can still be reversed afterward.
- [[Attribution Model]] — even a correctly-attributed conversion is subject to reversal.

---
*See also: [[Affiliate/Term|all affiliate terms]]*
