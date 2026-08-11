---
ai_hash: 32482949a852c228
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-03
entities: []
source: session 2026-07-03, vinnstack prd_comments 500
status: seedling
tags:
- postgres
- migrations
- vinnstack
- deployment
title: Per-feature migration scripts leave new tables silently missing until run
type: lesson
---

# Per-feature migration scripts leave new tables silently missing until run

When a repo's convention is "schema.sql holds the DDL, a per-feature scripts/migrate-*.mjs applies it on demand", shipping a new DB-backed feature does NOT make its tables exist - someone must run the migration against each target database. Until then every endpoint touching the new tables fails at runtime with `relation "<table>" does not exist`: reads surface as HTTP 500, writes as 400 (if the route maps store errors to 400), each taking a few seconds (connection setup + failed query), while tests stay green because they are mocked or DB-gated.

Recognition: a brand-new endpoint 500ing on FIRST use in an environment, ~2-3s latency, other DB endpoints fine.

Mitigations, cheapest first: (1) make "run the migration" an explicit rollout step in the feature's plan/commit message; (2) log the store error server-side (opErr) so the relation-does-not-exist message lands in the server console, not only the JSON body; (3) auto-apply the idempotent `IF NOT EXISTS` DDL at startup or on first query failure.

## Related

- [[Delete-and-reinsert aggregate saves silently cascade-wipe new child tables]]

%% ai-graph-start %%

**Related notes:**
- [[CREATE TABLE IF NOT EXISTS never upgrades existing tables - pair new columns with ALTER IF NOT EXISTS]]
- [[Idempotency guards keyed on object presence break when hydration materializes the object]]
- [[Delete-and-reinsert aggregate saves silently cascade-wipe new child tables]]
- [[Secondary-write failures should fail loud when their silent version was the actual bug]]
- [[Whole-aggregate read-modify-write for a per-child toggle causes lost updates under concurrent sibling writes]]

%% ai-graph-end %%