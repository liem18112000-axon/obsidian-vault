---
title: Monetag payments and payout terms
created: 2026-06-12
type: term
status: seedling
source: research session 2026-06-12 (third-party reviews; verify on monetag.com)
tags:
  - monetag
  - payments
  - payout
aliases:
  - Monetag minimum payout
  - Monetag payment methods
---

# Monetag payments and payout terms

**Monetag pays publishers weekly with a low entry barrier — a $5 minimum on most methods — which is one of its main draws over AdSense's higher, monthly threshold.** Exact terms drift, so treat these as recent (June 2026) reference points and confirm in-dashboard.

## Methods & thresholds

| Method | Minimum payout | Notes |
|---|---|---|
| PayPal | $5 | fees ~1–2%; recommended for speed |
| Paxum | $5 | |
| WebMoney | $5 | |
| Bank transfer | $5 | fees up to ~5% |
| Payoneer | activate at $30, then $20 threshold | higher bar |

## Schedule

- **Weekly**, paid on **Thursdays** (a net/hold period applies before the first payout).
- Sites with **10,000+ daily unique users** are auto-eligible for **weekly PayPal** payouts.

## Why it matters for automation

A weekly cadence + per-zone, per-country statistics ([[Monetag SSP API and integration]]) makes Monetag a good candidate for the same **scheduled-digest** pattern used for Accesstrade — pull the week's earnings by zone, summarize, and alert on drops. See [[Use case - automated daily conversion digest]].

## Related

- [[Monetag SSP API and integration]]
- [[Monetag referral program]]
- [[Monetag - MOC]]
