---
ai_hash: e448b2f6c867e0ff
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
aliases:
- Accesstrade rate limits
- Accesstrade pagination
created: 2026-06-11
entities: []
source: research session 2026-06-11
status: seedling
tags:
- affiliate
- accesstrade
- api
- rate-limit
- gotcha
title: Accesstrade API rate limits and pagination
type: term
---

# Accesstrade API rate limits and pagination

**Accesstrade meters its heavier reporting endpoints aggressively and paginates list endpoints, so any automation must page through results and back off rather than poll tightly.** The headline constraint comes from the OBS conversion report.

## The limits that bite

- **Conversion report (OBS):** documented at **1 request per 5 minutes**, and the `fromDate`/`toDate` window is capped at **7 days** per call. To cover a month you must make ~5 windowed calls, spaced ≥5 min apart.
- List endpoints return a **page** of rows; you must loop `page`/`limit` until a short/empty page signals the end.

## Pagination conventions by generation

| Concern | Classic `.vn` | OBS `.global` |
|---|---|---|
| Page controls | `page`, `limit` | `page`, `limit` (defaults page=1, limit=10) |
| Date range | `since`, `until` (required on txns/orders) | `fromDate`, `toDate` (ISO `yyyy-MM-ddTHH:mm:ss`, often URL-encoded with TZ) |
| Date semantics | by record time | `periodBase`: `CONVERSION_DATE` / `CONFIRMATION_DATE` / `POSTBACK_ERROR_DATE` / `UPDATED_DATE` |

## Design implications

```mermaid
flowchart TD
    A[Need 30 days of conversions] --> B{OBS 7-day cap}
    B --> C[Split into 5 windows]
    C --> D[Call window]
    D --> E[Wait >= 5 min<br/>1 req / 5 min]
    E --> F{More windows?}
    F -- yes --> D
    F -- no --> G[Merge + dedupe by conversionId]
```

- **Cache** report results locally; never re-pull the same window on every run — see [[Affiliate API engineering best practices]].
- Use `periodBase: UPDATED_DATE` to fetch only rows whose status *changed* since the last sync (incremental pulls), instead of re-reading the whole history.
- For a daily job, a single trailing-24h (or trailing-7d) window respects the cadence comfortably.

## Related

- [[Accesstrade conversion and transaction reporting]]
- [[Affiliate API engineering best practices]]
- [[Use case - automated daily conversion digest]]
- [[Accesstrade API Integration - MOC]]

%% ai-graph-start %%

**Related notes:**
- [[Accesstrade conversion and transaction reporting]]
- [[Affiliate API engineering best practices]]
- [[Accesstrade has two API generations]]
- [[To catch status flips, re-pull a lookback window wider than the report window]]
- [[Use case - automated daily conversion digest]]

%% ai-graph-end %%