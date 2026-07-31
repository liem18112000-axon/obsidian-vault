---
ai_hash: 42bc5bf95e1bf8a1
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-23
entities: []
source: session 2026-06-23 LUZ Detailnachweis aggregation
status: seedling
tags:
- design
- immutability
- gotcha
- java
title: Copy shared model objects before aggregating them for a view
type: lesson
---

# Copy shared model objects before aggregating them for a view

When you aggregate/transform a list of model objects for a *view* (e.g. summing volume & price for a report), **copy each representative object before mutating it** if the same list is shared with other consumers.

In the Luz detail-PDF aggregation, the `InvoiceItem`'s `Billing` list is also read by the main invoice document and the SAP booking export. The aggregator builds groups with a `copyBilling(...)` representative and sums subsequent records *into the copy* — so the originals' volume/price are never altered. A unit test asserts the source billings keep their original values.

**Why:** mutating shared model state to satisfy one view silently corrupts the others; bugs surface far from the cause.
**How to apply:** if a converter mutates inputs (this one already mutated `product.setTitles` for language filtering — a yellow flag), prefer copy-on-write for any field another flow relies on, and lock it in with a 'originals not mutated' assertion.

## Related
[[Luz Detailnachweis PDF aggregates billings by product reusing the AggregatedBilling key]]

## Related

- [[Luz Detailnachweis PDF aggregates billings by product reusing the AggregatedBilling key]]

%% ai-graph-start %%

**Related notes:**
- [[Luz Detailnachweis PDF aggregates billings by product reusing the AggregatedBilling key]]
- [[Use Lombok toBuilder for a shallow model copy, not manual setters or deep clone]]
- [[Aggregation collapses rows only if the key excludes per-record-unique fields]]
- [[Stimulsoft billingDetail mrt already had the MengeQuantity column and Calc columns]]

%% ai-graph-end %%