---
title: "Payrexx v1.0 charge API returns only status+message on failure — no ISO 8583 code"
created: 2026-07-29
type: observation
status: seedling
source: "session 2026-07-29; raw response filter on dev"
tags: [luz-157476, payrexx, decline-code, finding]
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

Related: [[LUZ-157476 decline-code flow: luz-online-payment forwards, luz_store maps]], [[Capture an unknown-named JSON field with Jackson @JsonAnySetter]]

## Related

- [[LUZ-157476 decline-code flow: luz-online-payment forwards]]
- [[luz_store maps]]
- [[Capture an unknown-named JSON field with Jackson @JsonAnySetter]]
