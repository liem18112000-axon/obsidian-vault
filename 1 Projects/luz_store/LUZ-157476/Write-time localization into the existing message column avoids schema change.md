---
title: "Write-time localization into the existing message column avoids schema change"
created: 2026-07-24
type: argument
status: seedling
source: "IMPLEMENTATION-PLAN refinement 2026-07-24"
tags: [luz-store, i18n, design-decision, taxonomy]
---

# Write-time localization into the existing message column avoids schema change

LUZ-157476 focused-scope decision: instead of persisting a failureCategory column and translating at read time, localize the customer message AT CHARGE TIME (customer's locale, bundle key per category) and write it into the existing invoice_item.message column at InvoiceRunServiceController:628 — the same write-time-localization mechanism the email path already uses at controller:893. Zero schema change, raw provider prose stops reaching the UI immediately.

Accepted tradeoffs: the locale is frozen at charge time (a customer who later switches language sees historic messages in the old locale) and the category is not SQL-queryable. Persisting the category stays a follow-up for reporting/re-translation. General lesson: when a display-localization feature is the goal, write-time localization into an existing free-text display field is the minimal-footprint first step; persist the structured code only when querying/re-rendering is actually needed.

## Related
- [[LUZ-157476 proposes seven failure categories with per-channel customer copy]]
- [[Invoice run v2 shows charge failures via verbatim message copy at controller line 628]]
- [[Persist payment failure category at charge time, never derive from prose]]
