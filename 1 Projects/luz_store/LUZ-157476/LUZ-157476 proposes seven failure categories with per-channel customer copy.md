---
ai_hash: 5ee6bdde62c7939c
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-24
entities:
- LUZ-157476
- failure categories
- customer copy
- Taxonomy proposal
- AC categories
- INSUFFICIENT_FUNDS
- CARD_EXPIRED
- BLOCKED_BY_ISSUER
- INVALID_DETAILS
- OTHER
- AUTH_EXPIRED
- NO_PAYMENT_METHOD
- Payrexx
- luz_store-local
- customer action
- retry policy
- email
- in-app
- hard declines
- LUZ-157476 decline taxonomy
- luz_online_payment boundary
- Observed Payrexx prose vocabulary
- TECHNICAL_ERROR
- invoice-item rerun
- pre-authorization
- codes
- taxonomy's purpose
- display
- Charge of pre-authorization failed.
- No Transaction found with id...
- Customer card payment this company does not exist
- channel
source: TAXONOMY-PROPOSAL.md 2026-07-24
status: budding
tags:
- luz-store
- taxonomy
- ux-copy
- design-decision
title: LUZ-157476 proposes seven failure categories with per-channel customer copy
type: model
---

# LUZ-157476 proposes seven failure categories with per-channel customer copy

Taxonomy proposal for LUZ-157476 (docs/luz-157476/TAXONOMY-PROPOSAL.md): the AC's five categories (INSUFFICIENT_FUNDS, CARD_EXPIRED, BLOCKED_BY_ISSUER, INVALID_DETAILS, OTHER) plus two data-justified extensions — AUTH_EXPIRED (pre-authorization no longer chargeable; Payrexx pre-auths expire ~5 days; covers the dominant 'Charge of pre-authorization failed.' and 'No Transaction found with id...' patterns) and NO_PAYMENT_METHOD (luz_store-local 'Customer card payment this company does not exist'). Rationale for extending: both have a customer action ('re-register card' / 'add a card') distinct from OTHER's 'try later', and actionability per category is the taxonomy's purpose.

Design principles worth reusing: match prose after stripping the provider prefix ('An error occurred:'), case-insensitive contains, OTHER as fallback; write copy per channel (email = full sentence with action, in-app = short label); category should drive retry policy, not just display — retry only OTHER/INSUFFICIENT_FUNDS, short-circuit hard declines to 'customer action required'.

## Related
- [[LUZ-157476 decline taxonomy maps codes at luz_online_payment boundary]]
- [[Observed Payrexx prose vocabulary in dev is only three messages]]
- [[TECHNICAL_ERROR is not retried in-flight but is retry-eligible on invoice-item rerun]]

%% ai-graph-start %%

**Related notes:**
- [[LUZ-157476 decline taxonomy maps codes at luz_online_payment boundary]]
- [[LUZ-157476 maps failure categories in luz_store only, overriding the boundary recommendation]]
- [[Persist payment failure category at charge time, never derive from prose]]
- [[Observed Payrexx prose vocabulary in dev is only three messages]]
- [[Payrexx card declines reach luz_store as ERROR with prose, not DECLINED]]

**Relations:**
- LUZ-157476 — *proposes* — failure categories
- failure categories — *has* — customer copy
- customer copy — *is* — per-channel
- Taxonomy proposal — *is for* — LUZ-157476
- Taxonomy proposal — *includes* — AC categories
- AC categories — *contains* — INSUFFICIENT_FUNDS
- AC categories — *contains* — CARD_EXPIRED
- AC categories — *contains* — BLOCKED_BY_ISSUER
- AC categories — *contains* — INVALID_DETAILS
- AC categories — *contains* — OTHER
- Taxonomy proposal — *extends with* — AUTH_EXPIRED
- Taxonomy proposal — *extends with* — NO_PAYMENT_METHOD
- AUTH_EXPIRED — *describes* — pre-authorization
- AUTH_EXPIRED — *covers* — Charge of pre-authorization failed.
- AUTH_EXPIRED — *covers* — No Transaction found with id...
- Payrexx — *issues* — pre-authorization
- pre-authorization — *has expiration* — ~5 days
- NO_PAYMENT_METHOD — *is associated with* — luz_store-local
- NO_PAYMENT_METHOD — *covers* — Customer card payment this company does not exist
- AUTH_EXPIRED — *requires* — customer action
- NO_PAYMENT_METHOD — *requires* — customer action
- OTHER — *requires* — customer action
- actionability per category — *is* — taxonomy's purpose
- failure categories — *drives* — retry policy
- failure categories — *drives* — display
- email — *is a* — channel
- in-app — *is a* — channel
- retry policy — *applies to* — OTHER
- retry policy — *applies to* — INSUFFICIENT_FUNDS
- hard declines — *require* — customer action
- LUZ-157476 decline taxonomy — *maps* — codes
- codes — *are at* — luz_online_payment boundary
- Observed Payrexx prose vocabulary — *has* — three messages
- TECHNICAL_ERROR — *is not* — retried in-flight
- TECHNICAL_ERROR — *is retry-eligible on* — invoice-item rerun
- LUZ-157476 — *is related to* — LUZ-157476 decline taxonomy
- Payrexx — *is related to* — Observed Payrexx prose vocabulary

%% ai-graph-end %%