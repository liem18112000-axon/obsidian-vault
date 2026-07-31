---
title: "Count fan-out _shard index must put _shard LAST in the compound key (ESR)"
created: 2026-06-23
type: lesson
status: seedling
source: "luz-docs LUZ-154613 session 2026-06-23"
tags: [mongodb, indexing, esr, luz-docs, count-fanout, performance]
---

# Count fan-out _shard index must put _shard LAST in the compound key (ESR)

For MongoDB count fan-out (split one count into K `_shard` range sub-counts), the index backing each sub-count must place the **range field `_shard` LAST** in a compound key, after the equality/filter prefix. This is the **ESR rule** (Equality, Sort, Range): index keys ordered equality-first, range-last, so each ranged sub-query seeks its slice *within* the narrowed prefix instead of scanning.

luz-docs `documents` collection, three indexes for the materialize count paths:
- `idx_shard` = `{ _shard: 1 }` — no-filter count
- `idx_isPublic_shard` = `{ _isPublic: 1, _shard: 1 }` (partialFilterExpression `{ _isPublic: true }`)
- `idx_effectiveSecurityClassCodes_shard` = `{ _effectiveSecurityClassCodes: 1, _shard: 1 }`

Without these, a fanned count does **K full scans** instead of K index range-seeks — fan-out then HURTS. Corollary from earlier benchmark: fan-out only helps while data fits the mongo cache; once disk-bound the index seeks still beat full scans but K stops scaling.

createIndex is idempotent on identical name+key (re-run = no-op). The `earchive-materialize-index` skill builds these and skips key-shape matches (FORCE=1 to recreate).

## Related

- [[MongoDB ESR rule]]
