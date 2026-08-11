---
ai_hash: f42c81b94768319d
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-12
entities: []
source: session 2026-06-12, accesstrade_integration code-review fixes
status: seedling
tags:
- api
- eventual-consistency
- reporting
- accesstrade
- gotcha
title: To catch status flips, re-pull a lookback window wider than the report window
type: lesson
---

# To catch status flips, re-pull a lookback window wider than the report window

**When an API only lets you filter by *creation* date (not last-modified) but records mutate after creation, the window you PULL must be wider than the window you REPORT — otherwise mutations to old records are never re-fetched and your local copy goes stale.** Separate the two windows explicitly in code.

Concrete case (Accesstrade conversion digest): the classic `/v1/transactions` filters by transaction (record) date — there is no UPDATED_DATE period basis (that's an OBS-only feature). A sale that converted 3 weeks ago but flipped PENDING→APPROVED yesterday will NOT be returned by a 1-day pull, so its stored row keeps a stale `updated_date` and a digest that reports on `updated_date` over yesterday misses the just-approved revenue.

Fix shape: give the build step two window pairs — an *ingest* window (wide, e.g. trailing 45 days, covering the original creation dates of anything that might still mutate) and a *report* window (narrow, what you actually summarize). Re-pull the wide window, upsert by id so flips overwrite, then aggregate the narrow window on the modified-date column locally.

```python
def run(self, since, until, *, ingest_since=None, ingest_until=None, date_col='updated_date'):
    self.ingest(ingest_since or since, ingest_until or until)   # WIDE pull
    return self.aggregate(since, until, date_col=date_col)      # NARROW report
```

The lookback width is a function of how long records stay mutable (here, how long conversions stay PENDING before approval/rejection). Relates to [[Idempotent link minting with content-hash cache keys]] (upsert-by-id is what makes the wide re-pull cheap and correct) and the Accesstrade reporting notes.

## Related

- [[Idempotent link minting with content-hash cache keys]]
- [[Affiliate API engineering best practices]]

%% ai-graph-start %%

**Related notes:**
- [[Affiliate API engineering best practices]]
- [[Accesstrade API rate limits and pagination]]
- [[Idempotent link minting with content-hash cache keys]]
- [[Accesstrade conversion and transaction reporting]]

%% ai-graph-end %%