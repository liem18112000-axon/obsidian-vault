---
title: "Stimulsoft billingDetail mrt already had the Menge/Quantity column and Calc columns"
created: 2026-06-23
type: lesson
status: seedling
source: "session 2026-06-23 LUZ Detailnachweis aggregation"
tags: [stimulsoft, reporting, luz_store, gotcha]
---

# Stimulsoft billingDetail mrt already had the Menge/Quantity column and Calc columns

When implementing product aggregation for the Luz invoice detail PDF, the assumption (from the ticket) was that the Stimulsoft template would need a new version with an added quantity column. **It didn't** — `src/main/resources/reporting/billingDetail20251212.mrt` already had everything:

- A **Menge / Quantity** column bound to `{root_billings.volume}` in the detail band, with header translations for de/en/fr/it in `GlobalizationStrings`.
- **Calc columns** computed in-template from raw Billing fields, so the JSON only needs the plain model fields:
  - `priceIncl` = `vatIncluded == true ? price : 0`
  - `priceExcl` = `vatIncluded == false ? price : 0`
  - `periode` = `consumptionDate.ToString("yyyy.MM")`
  - `Bezeichnung` = product title (+ date for LOHNABRECHNUNG)

So the whole change was Java-side (aggregate the billings list); **no .mrt and no message-bundle changes**.

**Gotcha:** a `.mrt` is JSON text — you can grep it. Fields the template binds that aren't on the Java model are usually Stimulsoft `Ident: Calc` columns with an `Expression`, derived from the real serialized fields. Verify the template before assuming it needs editing.

## Related
[[Luz Detailnachweis PDF aggregates billings by product reusing the AggregatedBilling key]]

## Related

- [[Luz Detailnachweis PDF aggregates billings by product reusing the AggregatedBilling key]]
