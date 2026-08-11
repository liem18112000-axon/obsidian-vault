---
ai_hash: d432f413d7997530
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-24
entities: []
source: session 2026-06-24 copyBilling -> toBuilder
status: seedling
tags:
- java
- lombok
- copy
- gotcha
title: Use Lombok toBuilder for a shallow model copy, not manual setters or deep clone
type: lesson
---

# Use Lombok toBuilder for a shallow model copy, not manual setters or deep clone

When you need an independent copy of a Lombok `@Builder` model to mutate a few fields without touching the original, prefer **`@Builder(toBuilder = true)` + `source.toBuilder().build()`** over a hand-written field-by-field copy or a deep clone.

**Why not a manual copy:** 30+ `copy.setX(source.getX())` lines silently go stale — add a field to the model and the copy quietly drops it.

**Why not `SerializationUtils.clone` (commons-lang3):** it's a *deep* clone of the whole object graph. In the Luz Billing case the copy must stay *shallow* (the `product` reference is shared, never mutated); a deep clone would needlessly duplicate the entire Product graph and throws `SerializationException` if any nested field isn't Serializable. Deep clone solves a problem we don't have and adds runtime risk.

**`toBuilder().build()` is a shallow copy** (each field reference copied as-is, including `product`), one line, no reflection, no serialization. It auto-includes any future field. Used in luz_store `InvoiceRunV2Converter.mergeGroup` to build the aggregated representative; precedent for the annotation already existed on `Document`/`InvoiceRunStatistics`/`TenantDunning`.

## Related
[[Copy shared model objects before aggregating them for a view]]
[[Luz Detailnachweis PDF aggregates billings by product reusing the AggregatedBilling key]]

## Related

- [[Copy shared model objects before aggregating them for a view]]

%% ai-graph-start %%

**Related notes:**
- [[Copy shared model objects before aggregating them for a view]]
- [[Luz Detailnachweis PDF aggregates billings by product reusing the AggregatedBilling key]]
- [[Capture an unknown-named JSON field with Jackson @JsonAnySetter]]
- [[javax.json JsonObject is immutable so aliasing replaces defensive deep copies]]

%% ai-graph-end %%