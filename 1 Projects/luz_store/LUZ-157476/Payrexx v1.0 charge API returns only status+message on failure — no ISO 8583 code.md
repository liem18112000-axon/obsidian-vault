---
ai_hash: 125689fc047bdcc4
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-29
entities:
- Payrexx v1.0 charge API
- ISO 8583 code
- POST /v1.0/Transaction/{id}
- status
- message
- data
- code
- ClientResponseFilter
- luz-online-payment Payrexx client
- LUZ-157476
- Payrexx
- Transaction.additionalProperties
- PayrexxResponse.additionalProperties
- Jackson @JsonAnySetter
- KlaraTransactionRequest.declineCode
- luz_store
- FailureCategory
- expir keyword
- api.klarapay.ch/v1.0
- Payrexx BACK-OFFICE transaction detail view
- CARD_EXPIRED
- declineCode plumbing
- decline-code flow
- Capture an unknown-named JSON field
source: session 2026-07-29; raw response filter on dev
status: seedling
tags:
- luz-157476
- payrexx
- decline-code
- finding
title: Payrexx v1.0 charge API returns only status+message on failure — no ISO 8583
  code
type: observation
---

# Payrexx v1.0 charge API returns only status+message on failure — no ISO 8583 code

Empirically confirmed on dev (2026-07-29) against real Payrexx (api.klarapay.ch/v1.0): a failed charge via `POST /v1.0/Transaction/{id}` returns ONLY:

```
{"status":"error","message":"An error occurred: Your card has expired."}
```

No `data`, no code field — nothing else. Captured via a temporary ClientResponseFilter that logs the raw body on the luz-online-payment Payrexx client.

Consequences for LUZ-157476:
- The ISO 8583 two-digit decline code Payrexx support mentioned exists only in their BACK-OFFICE transaction detail view, NOT in this API response. So `Transaction.additionalProperties` / `PayrexxResponse.additionalProperties` (@JsonAnySetter capture) are legitimately empty — there is nothing to capture.
- Therefore `KlaraTransactionRequest.declineCode` stays null on this path, and luz_store CANNOT key `FailureCategory` off a code today — it must keep mapping from the free-text `message` prose (e.g. "Your card has expired." -> CARD_EXPIRED via the `expir` keyword).
- The declineCode plumbing built in luz-online-payment is still correct and future-proof: if Payrexx later exposes the code (a new field, an expand param, or a different endpoint), only the candidate-key list needs updating.

Open follow-up: ask Payrexx whether the code is retrievable via any API field / query param / other endpoint at all; if not, the code-based taxonomy is blocked upstream.

Related: [[1 Projects/luz_store/LUZ-157476/LUZ-157476 decline-code flow luz-online-payment forwards, luz_store maps]], [[Capture an unknown-named JSON field with Jackson @JsonAnySetter]]

## Related

- [[1 Projects/luz_store/LUZ-157476/LUZ-157476 decline-code flow luz-online-payment forwards, luz_store maps]]
- [[Capture an unknown-named JSON field with Jackson @JsonAnySetter]]

%% ai-graph-start %%

**Related notes:**
- [[LUZ-157476 decline-code flow luz-online-payment forwards, luz_store maps]]
- [[luz_online_payment silently drops Payrexx decline codes]]
- [[LUZ-157476 decline taxonomy maps codes at luz_online_payment boundary]]
- [[KlaraPay DTOs are code-blind - lenient Jackson drops any Payrexx decline code]]
- [[Enumerate real Payrexx decline codes via chargeTransactionId lookup, not via service responses]]

**Relations:**
- Payrexx v1.0 charge API — *returns on failure* — status
- Payrexx v1.0 charge API — *returns on failure* — message
- Payrexx v1.0 charge API — *does not return on failure* — ISO 8583 code
- POST /v1.0/Transaction/{id} — *is part of* — Payrexx v1.0 charge API
- POST /v1.0/Transaction/{id} — *returns on failure* — status
- POST /v1.0/Transaction/{id} — *returns on failure* — message
- POST /v1.0/Transaction/{id} — *does not return on failure* — data
- POST /v1.0/Transaction/{id} — *does not return on failure* — code
- ClientResponseFilter — *logs raw body on* — luz-online-payment Payrexx client
- ISO 8583 code — *exists in* — Payrexx BACK-OFFICE transaction detail view
- ISO 8583 code — *is not in* — Payrexx v1.0 charge API response
- Transaction.additionalProperties — *are* — empty
- PayrexxResponse.additionalProperties — *are* — empty
- Jackson @JsonAnySetter — *captures* — Transaction.additionalProperties
- Jackson @JsonAnySetter — *captures* — PayrexxResponse.additionalProperties
- KlaraTransactionRequest.declineCode — *stays* — null
- luz_store — *cannot key* — FailureCategory
- luz_store — *maps from* — message
- message — *maps to* — FailureCategory
- message — *mapped to CARD_EXPIRED via* — expir keyword
- CARD_EXPIRED — *is a* — FailureCategory
- declineCode plumbing — *is built in* — luz-online-payment
- declineCode plumbing — *is* — correct and future-proof
- LUZ-157476 — *is related to* — decline-code flow
- LUZ-157476 — *is related to* — luz-online-payment
- LUZ-157476 — *is related to* — luz_store
- Jackson @JsonAnySetter — *is related to* — Capture an unknown-named JSON field
- Payrexx v1.0 charge API — *accessed via* — api.klarapay.ch/v1.0
- Payrexx — *provides* — Payrexx v1.0 charge API
- Payrexx — *provides* — Payrexx BACK-OFFICE transaction detail view
- luz-online-payment — *forwards* — decline-code flow
- luz_store — *maps* — decline-code flow

%% ai-graph-end %%