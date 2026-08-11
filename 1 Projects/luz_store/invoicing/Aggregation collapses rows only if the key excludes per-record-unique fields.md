---
ai_hash: 8592b94dd3ee2410
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-24
entities:
- Aggregation
- Grouping Key
- Per-record-unique fields
- MediData Detailnachweis
- AggregatedBilling
- cost_center
- EPOSTAPI_LETTER billing record
- PROD data
- Invoice detail PDF
- SAP booking
- product
- unit_price
- vat
- consumption_date
- PO decision
- Code decision
- Luz Detailnachweis PDF
- High-cardinality differentiator
- High-volume single product
source: session 2026-06-24 MediData EPOST cost_center finding
status: seedling
tags:
- luz_store
- invoicing
- aggregation
- gotcha
- sql
title: Aggregation collapses rows only if the key excludes per-record-unique fields
type: lesson
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

%% ai-graph-start %%

**Related notes:**
- [[Luz Detailnachweis PDF aggregates billings by product reusing the AggregatedBilling key]]
- [[Stimulsoft billingDetail mrt already had the MengeQuantity column and Calc columns]]
- [[Copy shared model objects before aggregating them for a view]]

**Relations:**
- Aggregation — *collapses rows if key excludes* — Per-record-unique fields
- Grouping Key — *excludes* — Per-record-unique fields
- Grouping Key — *reduces* — row count
- MediData Detailnachweis — *had* — page-explosion
- Key — *for* — Invoice detail PDF
- Key — *mirrored* — AggregatedBilling
- Key — *included* — cost_center
- EPOSTAPI_LETTER billing record — *has distinct* — cost_center
- EPOSTAPI_LETTER billing record — *in* — PROD data
- Grouping by key — *collapses* — nothing
- Aggregation key — *requires checking* — cardinality
- Key — *is useful for collapsing if* — High-cardinality differentiator
- High-cardinality differentiator — *is left out from* — Key
- Collapsing EPOST letters — *requires dropping* — cost_center
- Collapsing EPOST letters — *requires dropping* — consumption_date
- Aggregated line — *cannot display* — per-record cost center
- Detail on Detailnachweis — *is* — PO decision
- Detail on Detailnachweis — *is not* — Code decision
- AggregatedBilling key — *was built for* — SAP booking
- SAP booking — *uses* — cost_center
- Records — *share* — cost_center
- Records — *in* — SAP booking
- High-volume single product — *has* — cost_center
- cost_center — *as* — unique id
- Luz Detailnachweis PDF — *aggregates billings by* — product
- Luz Detailnachweis PDF — *reuses* — AggregatedBilling key

%% ai-graph-end %%