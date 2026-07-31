---
title: "LUZ-157476 proposes seven failure categories with per-channel customer copy"
created: 2026-07-24
type: model
status: budding
source: "TAXONOMY-PROPOSAL.md 2026-07-24"
tags: [luz-store, taxonomy, ux-copy, design-decision]
---

# LUZ-157476 proposes seven failure categories with per-channel customer copy

Taxonomy proposal for LUZ-157476 (docs/luz-157476/TAXONOMY-PROPOSAL.md): the AC's five categories (INSUFFICIENT_FUNDS, CARD_EXPIRED, BLOCKED_BY_ISSUER, INVALID_DETAILS, OTHER) plus two data-justified extensions — AUTH_EXPIRED (pre-authorization no longer chargeable; Payrexx pre-auths expire ~5 days; covers the dominant 'Charge of pre-authorization failed.' and 'No Transaction found with id...' patterns) and NO_PAYMENT_METHOD (luz_store-local 'Customer card payment this company does not exist'). Rationale for extending: both have a customer action ('re-register card' / 'add a card') distinct from OTHER's 'try later', and actionability per category is the taxonomy's purpose.

Design principles worth reusing: match prose after stripping the provider prefix ('An error occurred:'), case-insensitive contains, OTHER as fallback; write copy per channel (email = full sentence with action, in-app = short label); category should drive retry policy, not just display — retry only OTHER/INSUFFICIENT_FUNDS, short-circuit hard declines to 'customer action required'.

## Related
- [[LUZ-157476 decline taxonomy maps codes at luz_online_payment boundary]]
- [[Observed Payrexx prose vocabulary in dev is only three messages]]
- [[TECHNICAL_ERROR is not retried in-flight but is retry-eligible on invoice-item rerun]]
