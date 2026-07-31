---
title: "Per-feature migration scripts leave new tables silently missing until run"
created: 2026-07-03
type: lesson
status: seedling
source: "session 2026-07-03, vinnstack prd_comments 500"
tags: [postgres, migrations, vinnstack, deployment]
---

# Per-feature migration scripts leave new tables silently missing until run

When a repo's convention is "schema.sql holds the DDL, a per-feature scripts/migrate-*.mjs applies it on demand", shipping a new DB-backed feature does NOT make its tables exist - someone must run the migration against each target database. Until then every endpoint touching the new tables fails at runtime with `relation "<table>" does not exist`: reads surface as HTTP 500, writes as 400 (if the route maps store errors to 400), each taking a few seconds (connection setup + failed query), while tests stay green because they are mocked or DB-gated.

Recognition: a brand-new endpoint 500ing on FIRST use in an environment, ~2-3s latency, other DB endpoints fine.

Mitigations, cheapest first: (1) make "run the migration" an explicit rollout step in the feature's plan/commit message; (2) log the store error server-side (opErr) so the relation-does-not-exist message lands in the server console, not only the JSON body; (3) auto-apply the idempotent `IF NOT EXISTS` DDL at startup or on first query failure.

## Related

- [[Delete-and-reinsert aggregate saves silently cascade-wipe new child tables]]
