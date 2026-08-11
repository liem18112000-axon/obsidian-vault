---
ai_hash: da3b9f5d65f2c31b
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-23
entities: []
source: LUZ-157476 open-questions analysis 2026-07-23
status: seedling
tags:
- payments
- data-modeling
- design-decision
title: Persist payment failure category at charge time, never derive from prose
type: lesson
---

# Persist payment failure category at charge time, never derive from prose

A payment-failure category (insufficient-funds, card-expired, ...) is a fact about the charge attempt at the moment it happened — persist it as its own column when the charge result is stored, next to the raw error message. Do not derive it later by parsing prose error strings.

Why: prose messages are owned by upstream systems (payment provider / gateway wrapper) and can be reworded at any time — a derive-on-read mapping silently re-categorizes historical records when wording drifts, and every consumer must reimplement the mapping. A persisted category is stable over mapping versions, trivially queryable, and gives failure-distribution reporting for free. Cost is one migration + a write-path change.

Applied in LUZ-157476 (proposal: `failureCategory` column on INVOICE_CREDIT_CARD_TRANSACTION), but the principle generalizes to any derived classification of external free-text.

## Related
- [[LUZ-157476 decline taxonomy maps codes at luz_online_payment boundary]]
- [[luz_store TransactionStatus mirrors Payrexx API statuses plus two Klara-only values]]

%% ai-graph-start %%

**Related notes:**
- [[LUZ-157476 decline taxonomy maps codes at luz_online_payment boundary]]
- [[LUZ-157476 maps failure categories in luz_store only, overriding the boundary recommendation]]
- [[LUZ-157476 proposes seven failure categories with per-channel customer copy]]
- [[LUZ-157476 decline-code flow luz-online-payment forwards, luz_store maps]]
- [[Write-time localization into the existing message column avoids schema change]]

%% ai-graph-end %%