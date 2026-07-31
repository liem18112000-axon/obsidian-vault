---
ai_hash: d51514444cacfcf1
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
aliases:
- Cookie Duration
- Cookie Window
- Cookie Length
- Cookie Lifespan
- Referral Window
domain: affiliate-marketing
entities: []
tags:
- affiliate
- tracking
type: term
---

# Cookie Duration

> [!summary]
> **Cookie Duration** (a.k.a. cookie window) is how long after a visitor clicks your affiliate link the resulting action still earns you a commission. When the click happens, a tracking **cookie** stores your affiliate ID and a timestamp; if the visitor completes the action before the cookie expires, the payout is credited to you. After expiry, you get nothing.

## Plain-English definition

People rarely buy the instant they click. They click your link today, think about it, and buy four days later. Cookie duration is the **grace period** that lets that delayed purchase still count as yours. A 30-day cookie means "anything they do within 30 days of clicking is credited to me."

## How it works, step by step

1. Visitor clicks your tracked link.
2. A cookie is written to their browser: `{affiliate_id, click_timestamp}`.
3. The clock starts.
4. If the visitor completes the action (sale, lead) **before** the cookie expires, the network attributes it to you.
5. If they complete it **after** expiry — or the cookie was cleared — you earn nothing, even though your link started the journey.

## Why duration length matters so much

Longer windows credit more **delayed conversions**, which directly raises your [[Earnings per Click]]:

| Cookie window | Effect on your earnings |
|---------------|-------------------------|
| 24 hours (e.g. Amazon) | Only near-instant buyers count; many delayed sales lost |
| 30 days | Industry standard; captures most considered purchases |
| 60–90 days | Captures slow deciders; great for high-[[3 Resources/Work-Side/Affiliate/Term/Average Order Value|AOV]] / B2B |
| Lifetime / unlimited | Every future action counts (rare; common in SaaS recurring) |

> [!tip] Longer cookie = more credited conversions = higher EPC
> Two offers with identical commission rates are *not* equal if one has a 24-hour cookie and the other 30 days. The longer window quietly earns more from the same traffic.

## Common gotchas

> [!warning] The cookie is fragile
> It can be wiped before the action completes, costing you the commission:
> - User **clears cookies** or browses in **incognito/private** mode.
> - User switches **device** (clicks on phone, buys on laptop).
> - Another affiliate's link is clicked later and **overwrites** yours under last-click [[Attribution Model|attribution]].
> - Browser **privacy features** (Safari ITP, Firefox ETP) shorten or block third-party cookies.

- **Click resets the timer (usually).** Many programs refresh the window on each new click; some don't — read the terms.
- **Last-click overwrite.** Under last-click [[Attribution Model]], a later affiliate's cookie replaces yours. Being the *last* touch before purchase is what pays.
- **Session vs persistent.** A few stingy programs use session-only cookies — they die when the browser closes.

## How cookie duration interacts with other terms

- Feeds [[Earnings per Click]] — longer windows raise EPC by crediting delayed conversions.
- Bounded by the [[Attribution Model]] — last-click means a competitor can still steal a within-window sale.
- Pairs with high [[Average Order Value]] niches — big-ticket purchases take longer to decide, so a long cookie matters more.

## When cookie duration should drive your choice

- Promoting **considered / high-AOV purchases** (furniture, electronics, B2B) where buyers deliberate for days — demand a long window.
- Comparing two otherwise-equal offers — the longer cookie wins.
- For **impulse / low-cost** products bought on the spot, a short cookie matters less.

## Related terms

- [[Attribution Model]] — decides who gets credit when multiple affiliates are in-window.
- [[Earnings per Click]] — the metric a longer cookie improves.
- [[Cost per Action]] / [[Cost per Sale]] / [[Cost per Lead]] — the payouts the cookie window gates.
- [[Average Order Value]] — high-AOV buys take longer, making cookie length more important.
- [[Reversal]] — even a credited in-window sale can later be clawed back.

---
*See also: [[3 Resources/Work-Side/Affiliate/Term|all affiliate terms]]*

%% ai-graph-start %%

**Related notes:**
- [[Term]]
- [[Attribution Model]]
- [[Cost per Sale]]
- [[Cost per Action]]
- [[Earnings per Click]]

%% ai-graph-end %%