---
title: "Luz Detailnachweis PDF aggregates billings by product reusing the AggregatedBilling key"
created: 2026-06-23
type: lesson
status: seedling
source: "session 2026-06-23 LUZ Detailnachweis aggregation"
tags: [luz_store, invoicing, billing, aggregation, stimulsoft]
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
