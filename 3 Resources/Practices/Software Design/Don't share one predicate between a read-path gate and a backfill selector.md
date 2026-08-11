---
ai_hash: e5e112a0160f670e
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-17
entities: []
source: luz_docs LUZ-154613 review 2026-06-17
status: seedling
tags:
- code-review
- migrations
- materialized-view
- gotcha
title: Don't share one predicate between a read-path gate and a backfill selector
type: lesson
---

# Don't share one predicate between a read-path gate and a backfill selector

When a feature adds a per-document field that only a background **backfill** needs (e.g. `_shard` for parallel-count fan-out), do NOT add a "field is missing" clause for it to the predicate that already answers **"is this tenant fully migrated?"** if that same predicate also gates the **read path**.

In luz_docs the `buildUnmaterializedFilter()` (OR of missing `_isPublic` / `_effectiveSecurityClassCodes` / `_folderNames`) had two callers:
- `isMaterialized()` → drives `shouldUseMaterialized()` → gates the **entire** materialized (fast) read path.
- the migration backfill → selects documents to (re)stamp.

Adding `missing _shard` to that one filter looked like a clean one-liner, but it would have flipped every already-materialized tenant to "incomplete" and silently reverted the whole read path to the slow legacy path until a full re-backfill finished. Wrong altitude.

**Fix:** split the predicate. Keep the read-path gate on the original sentinels; give the backfill its own filter that *also* selects docs missing the new field. The backfill then stamps the field on legacy docs without ever touching the readiness gate.

**General rule:** if one predicate answers both *"is the feature ready? (read path)"* and *"what still needs migrating? (backfill)"*, treat that as a smell. A field that only the migration cares about belongs only in the migration selector — split the two definitions rather than overloading the shared one.

Related: [[3 Resources/Data/MongoDB/Count fan-out _shard index must put _shard LAST in the compound key (ESR)]]

## Related

- [[3 Resources/Data/MongoDB/Count fan-out _shard index must put _shard LAST in the compound key (ESR)]]

%% ai-graph-start %%

**Related notes:**
- [[Fan-out gate and backfill filter must cover the same field set]]
- [[Fan-out count needs an explicit key-absent sub-count to stay exact during shard backfill]]
- [[luz_docs stamps _shard on create to keep sharding gate stable]]
- [[Count fan-out _shard index must put _shard LAST in the compound key (ESR)]]
- [[Materialize gate must require _shard or parallelized count undercounts]]

%% ai-graph-end %%