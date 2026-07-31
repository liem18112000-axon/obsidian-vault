---
title: "Payrexx publishes no catalog of API wrapper error messages"
created: 2026-07-24
type: observation
status: seedling
source: "web research 2026-07-24"
tags: [payrexx, documentation, api, luz-157476]
---

# Payrexx publishes no catalog of API wrapper error messages

Payrexx has no public exhaustive list of the API wrapper prose messages ('An error occurred: ...') returned in PayrexxResponse.message on failed charges — the developer docs (transaction, gateway, preauthorization, webhooks pages) document only the wrapper format with one example. The closest official source is the merchant payment-status page (https://docs.payrexx.com/merchant/english/payment/payment-status): full ISO 8583 code list with per-code descriptions, but those are backoffice codes/UI text, not 1:1 API strings.

To enumerate wrapper messages: ask Payrexx support directly (LUZ-157476 Phase 0.2), plus empirical DB inventory + backoffice code lookup per charge_transaction_id.

Unverified hypothesis worth checking: Payrexx pre-authorizations expire after ~5 days (issuer-dependent, per their preauthorization docs) — the dominant 'Charge of pre-authorization failed.' message (192x in dev) is plausibly mostly expired pre-auths, which would merit its own taxonomy category ('authorization expired — re-register card') instead of generic other.

## Related
- [[Payrexx decline codes are ISO 8583 issuer codes from the card-issuing bank]]
- [[Observed Payrexx prose vocabulary in dev is only three messages]]
