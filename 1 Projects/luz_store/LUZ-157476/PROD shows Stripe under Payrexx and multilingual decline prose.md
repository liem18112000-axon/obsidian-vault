---
ai_hash: 663c0600667fda35
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-24
entities:
- PROD
- Stripe
- Payrexx
- KlaraPay
- LUZ-157476
- PaymentIntent
- source_data
- card_velocity_exceeded
- Stripe support contact
- '''Belastung der Vorautorisierung fehlgeschlagen.'''
- '''Charge of pre-authorization failed.'''
- EN
- DE
- Dev
- declined
- card number incorrect
- insufficient funds
- expired
- does-not-support-purchase
- invalid account
- '''Amount must be at least CHF 0.50'''
- Klara
- luz_store/docs/luz-157476/TAXONOMY-PROPOSAL.md §2
- Observed Payrexx prose vocabulary in dev is only three messages
- Payrexx publishes no catalog of API wrapper error messages
- LUZ-157476 maps failure categories in luz_store only, overriding the boundary recommendation
- invoice_credit_card_transaction export
- Keyword mappers
- PROD vocabulary
- Dev vocabulary
- decline prose
- Stripe texts
- Klara-side sub-minimum charge bug
- Full mapped table
source: PROD AlloyDB export 2026-07-24
status: budding
tags:
- luz-store
- payrexx
- stripe
- i18n
- data
title: PROD shows Stripe under Payrexx and multilingual decline prose
type: observation
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

%% ai-graph-start %%

**Related notes:**
- [[LUZ-157476 decline taxonomy maps codes at luz_online_payment boundary]]
- [[Observed Payrexx prose vocabulary in dev is only three messages]]
- [[Payrexx card declines reach luz_store as ERROR with prose, not DECLINED]]
- [[KlaraTransactionRequest.message content is Payrexx prose or runtime exception text, never a mapped constant]]
- [[LUZ-157476 decline-code flow luz-online-payment forwards, luz_store maps]]

**Relations:**
- PROD — *shows* — Stripe
- PROD — *shows* — Payrexx
- PROD — *shows* — multilingual decline prose
- Stripe — *is PSP behind* — Payrexx
- Stripe — *is PSP behind* — KlaraPay
- Stripe texts — *pass through* — Payrexx
- Stripe texts — *pass through* — KlaraPay
- PaymentIntent — *is example of* — Stripe texts
- source_data — *is example of* — Stripe texts
- card_velocity_exceeded — *is example of* — Stripe texts
- Stripe support contact — *is contained in* — Stripe texts
- Stripe texts — *are* — customer-visible
- decline prose — *is* — locale-dependent
- 'Belastung der Vorautorisierung fehlgeschlagen.' — *is German variant of* — 'Charge of pre-authorization failed.'
- Keyword mappers — *must cover* — EN
- Keyword mappers — *must cover* — DE
- Dev vocabulary — *is subset of* — PROD vocabulary
- PROD invoice_credit_card_transaction export — *revealed facts for* — LUZ-157476
- PROD — *shows top volume for* — declined
- PROD — *shows top volume for* — card number incorrect
- PROD — *shows top volume for* — insufficient funds
- PROD — *shows top volume for* — expired
- PROD — *shows top volume for* — does-not-support-purchase
- PROD — *shows top volume for* — invalid account
- 'Amount must be at least CHF 0.50' — *surfaced in* — PROD
- 'Amount must be at least CHF 0.50' — *suggests* — Klara-side sub-minimum charge bug
- Full mapped table — *is located at* — luz_store/docs/luz-157476/TAXONOMY-PROPOSAL.md §2
- LUZ-157476 — *is related to* — Observed Payrexx prose vocabulary in dev is only three messages
- LUZ-157476 — *is related to* — Payrexx publishes no catalog of API wrapper error messages
- LUZ-157476 — *is related to* — LUZ-157476 maps failure categories in luz_store only, overriding the boundary recommendation

%% ai-graph-end %%