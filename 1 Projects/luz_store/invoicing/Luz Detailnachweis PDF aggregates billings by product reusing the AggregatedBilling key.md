---
ai_hash: 8e38930b151022b0
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-23
entities:
- Luz Detailnachweis PDF
- AggregatedBilling key
- Billing record
- Medidata
- 2026 invoice run
- InvoiceRunV2Converter
- convertToInvoiceRun
- InvoiceDetailData
- SAP booking export
- InvoiceXpertlineDataExporter
- DB-level AggregatedBilling group-by
- Grouping key
- AggregatedBilling fields
- productId
- pricePlan
- featurePricePlan
- vatRate
- vatIncluded
- endToEndId
- invoiceNumber
- promotionCode
- consultantName
- costCenter
- origin
- productVariantCode
- unitPrice
- volume
- amount
- price
- consumptionDate
- billedFrom
- billedTo
- Stimulsoft billingDetail mrt
- MengeQuantity column
- Calc columns
- shared model objects
- main invoice document
- presentation-level aggregation
- raw billings
- product
- PDF generation failure
source: session 2026-06-23 LUZ Detailnachweis aggregation
status: seedling
tags:
- luz_store
- invoicing
- billing
- aggregation
- stimulsoft
title: Luz Detailnachweis PDF aggregates billings by product reusing the AggregatedBilling
  key
type: lesson
---

# Luz Detailnachweis PDF aggregates billings by product reusing the AggregatedBilling key

The invoice **Detailnachweis** (detail-proof) PDF used to render **one row per raw `Billing` record**, so a high-volume client (Medidata case, 2026 invoice run) with thousands of records of the same product produced a 400+ page PDF that failed to generate — blocking the whole invoice.

The fix is a **presentation-level aggregation**: group the item's billings by product in `InvoiceRunV2Converter.convertToInvoiceRun(...)` *before* building `InvoiceDetailData`, so each product appears once with summed volume (quantity) and summed amount.

Crucially, the main invoice document and the SAP booking export (`InvoiceXpertlineDataExporter`) **already** collapse records via the DB-level `AggregatedBilling` group-by — only the detail PDF used raw billings. So reusing the *same key* guarantees the detail PDF collapses to the same line count the invoice already produced, and totals are unchanged.

Grouping key = AggregatedBilling fields (productId, pricePlan, featurePricePlan, vatRate, vatIncluded, endToEndId, invoiceNumber, promotionCode, consultantName, costCenter, origin) **plus** productVariantCode + unitPrice. Aggregates: sum(volume), sum(price), min(consumptionDate), min(billedFrom), max(billedTo).

**Why:** aggregation only changes presentation; merging is safe only when price-relevant attributes match, otherwise lines stay separate.
**How to apply:** when a per-record report explodes for high-volume tenants, check whether a sibling flow (invoice/booking) already aggregates and reuse its exact key.

## Related
[[3 Resources/Visual/Stimulsoft/Stimulsoft billingDetail mrt already had the MengeQuantity column and Calc columns]]
[[Copy shared model objects before aggregating them for a view]]

## Related

- [[3 Resources/Visual/Stimulsoft/Stimulsoft billingDetail mrt already had the MengeQuantity column and Calc columns]]
- [[Copy shared model objects before aggregating them for a view]]

%% ai-graph-start %%

**Related notes:**
- [[Aggregation collapses rows only if the key excludes per-record-unique fields]]
- [[Stimulsoft billingDetail mrt already had the MengeQuantity column and Calc columns]]
- [[Copy shared model objects before aggregating them for a view]]
- [[Use Lombok toBuilder for a shallow model copy, not manual setters or deep clone]]

**Relations:**
- Luz Detailnachweis PDF — *aggregates by* — product
- Luz Detailnachweis PDF — *reused* — AggregatedBilling key
- Luz Detailnachweis PDF — *previously rendered* — Billing record
- Billing record — *caused* — PDF generation failure
- Medidata — *is a* — high-volume client
- 2026 invoice run — *involved* — Medidata
- presentation-level aggregation — *is a* — fix
- InvoiceRunV2Converter — *contains method* — convertToInvoiceRun
- convertToInvoiceRun — *groups* — billings by product
- convertToInvoiceRun — *precedes building* — InvoiceDetailData
- main invoice document — *collapses records via* — DB-level AggregatedBilling group-by
- SAP booking export — *collapses records via* — DB-level AggregatedBilling group-by
- SAP booking export — *is also known as* — InvoiceXpertlineDataExporter
- Luz Detailnachweis PDF — *previously used* — raw billings
- AggregatedBilling key — *ensures collapse of* — Luz Detailnachweis PDF
- Grouping key — *comprises* — AggregatedBilling fields
- Grouping key — *includes* — productVariantCode
- Grouping key — *includes* — unitPrice
- AggregatedBilling fields — *include* — productId
- AggregatedBilling fields — *include* — pricePlan
- AggregatedBilling fields — *include* — featurePricePlan
- AggregatedBilling fields — *include* — vatRate
- AggregatedBilling fields — *include* — vatIncluded
- AggregatedBilling fields — *include* — endToEndId
- AggregatedBilling fields — *include* — invoiceNumber
- AggregatedBilling fields — *include* — promotionCode
- AggregatedBilling fields — *include* — consultantName
- AggregatedBilling fields — *include* — costCenter
- AggregatedBilling fields — *include* — origin
- presentation-level aggregation — *sums* — volume
- presentation-level aggregation — *sums* — amount
- presentation-level aggregation — *sums* — price
- presentation-level aggregation — *finds min* — consumptionDate
- presentation-level aggregation — *finds min* — billedFrom
- presentation-level aggregation — *finds max* — billedTo
- presentation-level aggregation — *changes* — presentation
- Luz Detailnachweis PDF — *is related to* — Stimulsoft billingDetail mrt
- Stimulsoft billingDetail mrt — *contains* — MengeQuantity column
- Stimulsoft billingDetail mrt — *contains* — Calc columns
- Luz Detailnachweis PDF — *is related to* — Copy shared model objects before aggregating them for a view

%% ai-graph-end %%