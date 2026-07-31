---
ai_hash: 3a9cb7fedc985321
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-24
entities: []
source: web research 2026-07-24
status: seedling
tags:
- payrexx
- documentation
- api
- luz-157476
title: Payrexx publishes no catalog of API wrapper error messages
type: observation
---

# Payrexx publishes no catalog of API wrapper error messages

Payrexx has no public exhaustive list of the API wrapper prose messages ('An error occurred: ...') returned in PayrexxResponse.message on failed charges — the developer docs (transaction, gateway, preauthorization, webhooks pages) document only the wrapper format with one example. The closest official source is the merchant payment-status page (https://docs.payrexx.com/merchant/english/payment/payment-status): full ISO 8583 code list with per-code descriptions, but those are backoffice codes/UI text, not 1:1 API strings.

To enumerate wrapper messages: ask Payrexx support directly (LUZ-157476 Phase 0.2), plus empirical DB inventory + backoffice code lookup per charge_transaction_id.

Unverified hypothesis worth checking: Payrexx pre-authorizations expire after ~5 days (issuer-dependent, per their preauthorization docs) — the dominant 'Charge of pre-authorization failed.' message (192x in dev) is plausibly mostly expired pre-auths, which would merit its own taxonomy category ('authorization expired — re-register card') instead of generic other.

## Related
- [[Payrexx ISO 8583 decline code to meaning reference table]]
- [[Observed Payrexx prose vocabulary in dev is only three messages]]

%% ai-graph-start %%

**Related notes:**
- [[Payrexx delivers decline code only via webhook, not sync response]]
- [[Payrexx v1.0 charge API returns only status+message on failure — no ISO 8583 code]]
- [[Payrexx ISO 8583 decline code to meaning reference table]]
- [[KlaraTransactionRequest.message content is Payrexx prose or runtime exception text, never a mapped constant]]
- [[PROD shows Stripe under Payrexx and multilingual decline prose]]

%% ai-graph-end %%