---
title: "Aggregation collapses rows only if the key excludes per-record-unique fields"
created: 2026-06-24
type: lesson
status: seedling
source: "session 2026-06-24 MediData EPOST cost_center finding"
tags: [luz_store, invoicing, aggregation, gotcha, sql]
---

# Aggregation collapses rows only if the key excludes per-record-unique fields

A presentation-level aggregation only reduces row count if the grouping key excludes fields that are **unique per record**. Discovered while fixing the MediData Detailnachweis page-explosion: the implemented key for the invoice detail PDF mirrored `AggregatedBilling` and therefore included `cost_center` — but in MediData's PROD data **every `EPOSTAPI_LETTER` billing record has a distinct cost_center**. So grouping by that key collapses nothing (records_merged ≈ 1) and the 400+ page PDF would still fail.

**Lesson:** before trusting an aggregation key, check cardinality of each key field against the real data (`COUNT(DISTINCT field)` vs `COUNT(*)`). A key is only useful for collapsing if at least one high-cardinality differentiator is left OUT.

**Decision/trade-off:** to actually collapse EPOST letters, drop `cost_center` (and exact `consumption_date`) from the key and group by product + unit_price + vat — but then the aggregated line can no longer display a per-record cost center. Whether that detail is required on the Detailnachweis is a PO decision, not a code decision.

Reuse-the-precedent (`AggregatedBilling`) was the right instinct, but the precedent's key was built for SAP booking (where cost_center matters and records do share it), not for a high-volume single product where cost_center is effectively a unique id.

## Related
[[Luz Detailnachweis PDF aggregates billings by product reusing the AggregatedBilling key]]

## Related

- [[Luz Detailnachweis PDF aggregates billings by product reusing the AggregatedBilling key]]
