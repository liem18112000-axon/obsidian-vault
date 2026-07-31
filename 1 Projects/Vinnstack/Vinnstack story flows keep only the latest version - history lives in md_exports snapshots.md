---
title: "Vinnstack story flows keep only the latest version - history lives in md_exports snapshots"
created: 2026-07-23
type: observation
status: seedling
source: "session 2026-07-23"
tags: [vinnstack, database, versioning, gotcha]
---

# Vinnstack story flows keep only the latest version - history lives in md_exports snapshots

Vinnstack's `story_flows` table has PRIMARY KEY (epic, story_key) and a bare `version INT` that bumps on regenerate — the row is **overwritten in place**, so the previous flow markdown is gone from that table the moment a story's process flow is regenerated.

The only place historical flow versions survive is the **`md_exports` stories snapshots**: each snapshot (cut on submit/regenerate/manual) embeds every story's flow *at its then-current version*, with a `*Process flow — vN · status:*` marker line under the story heading. To recover story-flow vN, find the newest stories snapshot whose S-key section carries the `vN` marker and extract that section (awk between `^## S1 ` and the next `^## ` heading).

Corollary: PRD revisions get a real history table (`prd_revisions`) but story flows do not — if per-version flow comparison ever becomes a first-class feature, story_flows needs the same treatment.

## Related

- [[Async-enriched columns need a lazy backfill for pre-feature rows]]
