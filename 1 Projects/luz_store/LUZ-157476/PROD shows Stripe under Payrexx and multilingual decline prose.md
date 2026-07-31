---
title: "PROD shows Stripe under Payrexx and multilingual decline prose"
created: 2026-07-24
type: observation
status: budding
source: "PROD AlloyDB export 2026-07-24"
tags: [luz-store, payrexx, stripe, i18n, data]
---

# PROD shows Stripe under Payrexx and multilingual decline prose

PROD invoice_credit_card_transaction export (2026-07-24, 16 distinct patterns, all status ERROR) revealed three load-bearing facts for LUZ-157476:

1. Stripe is the PSP behind Payrexx/KlaraPay — verbatim Stripe texts pass through ('PaymentIntent', 'source_data', 'card_velocity_exceeded'-style wording), one message even contains https://support.stripe.com/contact and is customer-visible today via the verbatim message copy. The prose vocabulary therefore drifts with Payrexx's OWN vendor, not just Payrexx.
2. Decline prose is locale-dependent — 'Belastung der Vorautorisierung fehlgeschlagen.' (827x) is the German variant of 'Charge of pre-authorization failed.'. Keyword mappers must cover EN+DE minimum; fallback catches other locales.
3. Dev vocabulary (3 messages) was a tiny subset of PROD (16) — never freeze a message mapping on dev data alone.

Top PROD volumes: declined 8875, card number incorrect 6746, insufficient funds 5971, expired 4664, does-not-support-purchase 2697, invalid account 1473. Full mapped table: luz_store/docs/luz-157476/TAXONOMY-PROPOSAL.md §2. Also surfaced: 'Amount must be at least CHF 0.50' (13x) — possible Klara-side sub-minimum charge bug.

## Related
- [[Observed Payrexx prose vocabulary in dev is only three messages]]
- [[Payrexx publishes no catalog of API wrapper error messages]]
- [[LUZ-157476 maps failure categories in luz_store only, overriding the boundary recommendation]]
