---
type: term
domain: affiliate-marketing
aliases: [CPQL, Cost per Qualified Lead, Pay per Qualified Lead]
tags: [affiliate, pricing-model, metric]
---

# Cost per Quality Lead

> [!summary]
> **Cost per Quality Lead (CPQL)** is an affiliate pricing model where the advertiser pays only for leads that meet a defined **quality bar** — not for every form fill. It is a stricter variant of [[Cost per Lead]]: the lead must be validated as genuine, qualified, and likely to convert before it earns a payout.

## Plain-English definition

Under plain [[Cost per Lead]], you're paid for *any* lead that completes the action (a form, a sign-up). Under **CPQL**, the merchant adds a gate: the lead only counts — and only pays — if it is a **quality** lead by their criteria. A tyre-kicker who fills the form but fails the qualification check earns you nothing.

In short: **CPL pays for volume; CPQL pays for fit.**

## Why CPQL exists

Plain CPL has a built-in conflict: affiliates are rewarded for *quantity* of leads, merchants only profit from *quality* of leads. Affiliates chasing volume flood merchants with junk — bots, incentivised sign-ups, wrong-country prospects, people who'll never buy.

CPQL realigns the incentives. By paying only for leads that clear a quality bar, the merchant pushes the affiliate to **pre-qualify traffic** rather than maximise raw form fills.

> [!note] CPQL is CPL with a gate
> It's not a separate branch of the [[Cost per Action]] family — it's [[Cost per Lead]] plus an explicit quality filter applied before the payout is counted.

## What makes a lead "quality"?

The merchant defines this in the offer terms. Common criteria:

- **Qualification fields** — income above a threshold, valid phone, business email (not gmail), specific job title.
- **Geo / demographic fit** — lead is in a target country, age range, or credit tier.
- **Intent signals** — lead requested a callback, booked a demo, or completed a multi-step form (not just an email).
- **Verification** — phone or email confirmed (double opt-in), or the lead answered a follow-up call.
- **De-duplication** — not an existing customer or a previously submitted lead.

## How a CPQL payout works

1. Affiliate places a tracked link.
2. Visitor clicks; a [[Cookie Duration|cookie]] records the affiliate's ID.
3. Visitor submits the lead form.
4. **Quality gate** — the merchant scores/verifies the lead against its criteria (automated scrubbing, manual review, or a qualifying call).
5. Only **passing** leads are credited; the rest are rejected (no pay).
6. The flat **CPQL payout** is paid — higher per lead than plain CPL, because each one is pre-vetted.

## Worked example

A B2B SaaS offers **$120 per qualified lead**, where "qualified" = a booked demo with a company of 50+ employees.

- You drive 500 clicks, 80 book a demo (raw leads).
- The merchant's gate passes 30 of them (50+ employees, showed up to the call).
- Payout: `30 × $120 = $3,600`. The other 50 demos earn nothing.

Compare plain CPL at, say, $20 per *any* demo booked: `80 × $20 = $1,600`. CPQL pays more **if** you can source the right traffic — and far less if you can't.

## CPL vs CPQL at a glance

| | [[Cost per Lead]] (CPL) | Cost per Quality Lead (CPQL) |
|---|---|---|
| Pays for | Any completed lead | Only leads passing a quality bar |
| Per-lead payout | Lower | Higher |
| Rejection risk | Lower | Higher |
| Rewards affiliate for | Volume | Fit / pre-qualification |
| Best traffic | Broad | Targeted, high-intent |

## Common gotchas

> [!warning] You can do all the work and still not get paid
> Because the quality gate sits *after* the lead is submitted, a large share of your leads can be rejected — sometimes by **opaque or subjective** merchant criteria. Before committing traffic, pin down: exactly what "quality" means, who judges it, the typical pass rate, and the dispute process.

- **Pass-rate transparency** — ask the merchant for a historical qualified-rate so you can model real [[Earnings per Click]].
- **Subjective rejection** — manual review can be a backdoor for the merchant to underpay; favour offers with *objective, automated* criteria.
- **Longer hold periods** — qualification (especially a callback) takes time, delaying payout.
- **Attribution** — still typically last-click; see [[Attribution Model]].

## When CPQL is the right model to chase

- You can **target tightly** — your audience matches the merchant's ideal customer (right industry, income, geo).
- You'd rather earn more on fewer, well-fit leads than scrape volume.
- The merchant has a **high-value sale** (B2B, finance, insurance) that justifies a rich per-qualified-lead payout.

If your traffic is broad and you can't pre-qualify, plain [[Cost per Lead]] (or even [[Cost per Sale]]) may net more, because CPQL will reject too many of your leads.

## Related terms

- [[Cost per Lead]] — the parent model; CPQL adds a quality gate on top.
- [[Cost per Action]] — the umbrella family CPL/CPQL belong to.
- [[Cost per Sale]] — the purchase-based sibling.
- [[Earnings per Click]] — the metric to compare offers; for CPQL, must use the *qualified* pass rate.
- [[Cookie Duration]] — the window in which your referred lead still counts.
- [[Attribution Model]] — decides which affiliate gets credited.
- [[Reversal]] — clawback of leads that fail validation after being provisionally counted.

---
*See also: [[3 Resources/Work-Side/Affiliate/Term|all affiliate terms]]*
