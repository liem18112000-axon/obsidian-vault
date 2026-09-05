---
title: "Postgres GREATEST ignores NULLs — use it to advance a timestamp column forward only"
created: 2026-08-23
type: term
status: seedling
source: "LEO CDP resolver fix 2026-08-23"
tags: [postgresql, sql, idiom, timestamps]
---

# Postgres GREATEST ignores NULLs — use it to advance a timestamp column forward only

PostgreSQL `GREATEST(a, b, ...)` returns the largest **non-null** argument and only yields NULL when *every* argument is NULL (unlike `MAX`, which is an aggregate, `GREATEST` is a row-level function). This makes it the clean idiom for a **monotonic forward-only timestamp** update:

```sql
UPDATE t SET last_activity_at = GREATEST(last_activity_at, %s::timestamp) WHERE id = %s;
```

Behavior that falls out for free:
- existing NULL + incoming value -> takes the incoming value (first-ever stamp),
- existing value + incoming NULL -> keeps the existing value,
- both present -> the later one, so an out-of-order/older event never regresses a newer stored timestamp.

No `COALESCE`, no `CASE`, no `IS NULL` guards needed. Cast the bound param (`%s::timestamp`) so a `timestamp` column and a possibly tz-aware Python datetime resolve to one comparable type. Used in LEO CDP's CIR resolver merge path to advance `cdp_master_profiles.last_activity_at`.

Mirror caution: `LEAST` is the same but for the minimum; both skip NULLs.

See [[LEO CDP segment member_count is 0 because default rules filter on never-populated ML columns]].

## Related

- [[LEO CDP segment member_count is 0 because default rules filter on never-populated ML columns]]
