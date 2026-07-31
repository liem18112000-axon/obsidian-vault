---
title: "Persist payment failure category at charge time, never derive from prose"
created: 2026-07-23
type: lesson
status: seedling
source: "LUZ-157476 open-questions analysis 2026-07-23"
tags: [payments, data-modeling, design-decision]
---

# Persist payment failure category at charge time, never derive from prose

A payment-failure category (insufficient-funds, card-expired, ...) is a fact about the charge attempt at the moment it happened — persist it as its own column when the charge result is stored, next to the raw error message. Do not derive it later by parsing prose error strings.

Why: prose messages are owned by upstream systems (payment provider / gateway wrapper) and can be reworded at any time — a derive-on-read mapping silently re-categorizes historical records when wording drifts, and every consumer must reimplement the mapping. A persisted category is stable over mapping versions, trivially queryable, and gives failure-distribution reporting for free. Cost is one migration + a write-path change.

Applied in LUZ-157476 (proposal: `failureCategory` column on INVOICE_CREDIT_CARD_TRANSACTION), but the principle generalizes to any derived classification of external free-text.

## Related
- [[LUZ-157476 decline taxonomy maps codes at luz_online_payment boundary]]
- [[luz_store TransactionStatus mirrors Payrexx API statuses plus two Klara-only values]]
