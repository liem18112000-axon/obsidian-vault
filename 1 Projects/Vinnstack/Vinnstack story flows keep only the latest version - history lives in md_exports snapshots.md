---
ai_hash: 09b2b44b6356c51c
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-23
entities:
- Vinnstack
- story_flows
- epic
- story_key
- version
- flow markdown
- md_exports
- stories snapshots
- historical flow versions
- S-key section
- PRD revisions
- prd_revisions
- story flows
- Async-enriched columns
- lazy backfill
- pre-feature rows
source: session 2026-07-23
status: seedling
tags:
- vinnstack
- database
- versioning
- gotcha
title: Vinnstack story flows keep only the latest version - history lives in md_exports
  snapshots
type: observation
---

# Vinnstack story flows keep only the latest version - history lives in md_exports snapshots

Vinnstack's `story_flows` table has PRIMARY KEY (epic, story_key) and a bare `version INT` that bumps on regenerate — the row is **overwritten in place**, so the previous flow markdown is gone from that table the moment a story's process flow is regenerated.

The only place historical flow versions survive is the **`md_exports` stories snapshots**: each snapshot (cut on submit/regenerate/manual) embeds every story's flow *at its then-current version*, with a `*Process flow — vN · status:*` marker line under the story heading. To recover story-flow vN, find the newest stories snapshot whose S-key section carries the `vN` marker and extract that section (awk between `^## S1 ` and the next `^## ` heading).

Corollary: PRD revisions get a real history table (`prd_revisions`) but story flows do not — if per-version flow comparison ever becomes a first-class feature, story_flows needs the same treatment.

## Related

- [[Async-enriched columns need a lazy backfill for pre-feature rows]]

%% ai-graph-start %%

**Related notes:**
- [[Per-key write lock for parallel aggregate writes; self-migrating column via idempotent ALTER]]
- [[Async-enriched columns need a lazy backfill for pre-feature rows]]
- [[Version artifacts by lifecycle event with content-dedupe, store in DB not files]]
- [[Vinnstack interrogationStore full-aggregate rewrite loses concurrent updates to the same epic]]
- [[vinnstack pipeline skills share one slicing model - flipping it cascades]]

**Relations:**
- Vinnstack — *has table* — story_flows
- story_flows — *has PRIMARY KEY* — (epic, story_key)
- story_flows — *has column* — version
- version — *is type* — INT
- version — *bumps on* — regenerate
- story_flows — *row is overwritten in place on* — regenerate
- previous flow markdown — *is gone from* — story_flows
- historical flow versions — *survive in* — md_exports
- md_exports — *are* — stories snapshots
- stories snapshots — *embeds* — every story's flow
- stories snapshots — *embeds flow at* — its then-current version
- stories snapshots — *cut on* — submit/regenerate/manual
- stories snapshots — *has marker line* — *Process flow — vN · status:*
- stories snapshots — *contains* — S-key section
- PRD revisions — *get history table* — prd_revisions
- story flows — *do not get history table like* — prd_revisions
- Async-enriched columns — *need* — lazy backfill
- lazy backfill — *is for* — pre-feature rows

%% ai-graph-end %%