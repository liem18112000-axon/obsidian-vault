---
ai_hash: 49ce985adcc5e34c
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-24
entities:
- LUZ-157476
- write-time localization
- schema change
- invoice_item.message column
- customer message
- charge time
- customer's locale
- bundle key
- failureCategory column
- InvoiceRunServiceController:628
- email path
- controller:893
- UI
- locale
- category
- SQL-queryable
- reporting
- re-translation
- display-localization feature
- free-text display field
- structured code
- Invoice run v2
- charge failures
- verbatim message copy
- payment failure category
- raw provider prose
- seven failure categories
- prose
source: IMPLEMENTATION-PLAN refinement 2026-07-24
status: seedling
tags:
- luz-store
- i18n
- design-decision
- taxonomy
title: Write-time localization into the existing message column avoids schema change
type: argument
---

# Write-time localization into the existing message column avoids schema change

LUZ-157476 focused-scope decision: instead of persisting a failureCategory column and translating at read time, localize the customer message AT CHARGE TIME (customer's locale, bundle key per category) and write it into the existing invoice_item.message column at InvoiceRunServiceController:628 — the same write-time-localization mechanism the email path already uses at controller:893. Zero schema change, raw provider prose stops reaching the UI immediately.

Accepted tradeoffs: the locale is frozen at charge time (a customer who later switches language sees historic messages in the old locale) and the category is not SQL-queryable. Persisting the category stays a follow-up for reporting/re-translation. General lesson: when a display-localization feature is the goal, write-time localization into an existing free-text display field is the minimal-footprint first step; persist the structured code only when querying/re-rendering is actually needed.

## Related
- [[LUZ-157476 proposes seven failure categories with per-channel customer copy]]
- [[Invoice run v2 shows charge failures via verbatim message copy at controller line 628]]
- [[Persist payment failure category at charge time, never derive from prose]]

%% ai-graph-start %%

**Related notes:**
- [[Persist payment failure category at charge time, never derive from prose]]
- [[Invoice run v2 shows charge failures via verbatim message copy at controller line 628]]
- [[LUZ-157476 maps failure categories in luz_store only, overriding the boundary recommendation]]
- [[LUZ-157476 proposes seven failure categories with per-channel customer copy]]
- [[QR payment-method error message is a hardcoded English constant in luz_store]]

**Relations:**
- write-time localization — *avoids* — schema change
- write-time localization — *uses* — invoice_item.message column
- LUZ-157476 — *decided on* — write-time localization
- write-time localization — *localizes* — customer message
- customer message — *localized at* — charge time
- customer message — *uses* — customer's locale
- customer message — *uses* — bundle key
- write-time localization — *writes into* — invoice_item.message column
- invoice_item.message column — *updated at* — InvoiceRunServiceController:628
- failureCategory column — *is an alternative to* — write-time localization
- email path — *uses* — write-time localization
- email path — *uses mechanism at* — controller:893
- raw provider prose — *stops reaching* — UI
- locale — *is frozen at* — charge time
- category — *is not* — SQL-queryable
- category — *persisted for* — reporting
- category — *persisted for* — re-translation
- display-localization feature — *goal is* — write-time localization
- write-time localization — *uses* — free-text display field
- structured code — *persisted for* — querying
- structured code — *persisted for* — re-rendering
- LUZ-157476 — *proposes* — seven failure categories
- Invoice run v2 — *shows* — charge failures
- charge failures — *via* — verbatim message copy
- verbatim message copy — *at* — InvoiceRunServiceController:628
- payment failure category — *persisted at* — charge time
- payment failure category — *not derived from* — prose

%% ai-graph-end %%