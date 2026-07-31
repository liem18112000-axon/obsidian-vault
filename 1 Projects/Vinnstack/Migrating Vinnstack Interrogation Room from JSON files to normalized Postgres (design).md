---
ai_hash: b3d6371dfeda3c7e
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-02
entities:
- Vinnstack Interrogation Room
- JSON files
- PostgreSQL
- Interrogation
- lib/interrogationStore.ts
- epic
- questions
- options
- answers
- visuals
- prd
- revisions
- stories
- flows
- Markdown mirror
- chat agent
- git auto-commit
- DB-only
- Sync
- async
- tsc
- app/api/interrogation/route.ts
- lib/interrogationRunner.ts
- transaction
- ultracodeRunner
- VAULT_DIR
- DATABASE_URL
- Docker postgres:16
- db/schema.sql
- doc/interrogation-persistence-plan.md
- 3 Resources/Work-Side/Vinnstack/Vinnstack auth providers two patterns and the rule
  for adding one
source: session 2026-07-02
status: seedling
tags:
- vinnstack
- postgres
- persistence
- migration
- interrogation-room
title: Migrating Vinnstack Interrogation Room from JSON files to normalized Postgres
  (design)
type: argument
---

# Migrating Vinnstack Interrogation Room from JSON files to normalized Postgres (design)

Vinnstack's Interrogation Room store (lib/interrogationStore.ts) is **aggregate-oriented**: the whole `Interrogation` (epic + questions + options + answers + visuals + prd + revisions + stories + flows) is always loaded whole, mutated in memory, and saved whole. Persistence was one JSON file per epic + a Markdown mirror the chat agent reads + git auto-commit.

Decision (2026-07-02, branch feature/interrogation-persistence): move to **PostgreSQL, fully normalized, DB-only** (drop the .json + .md files).

Key implications captured for the implementation:
- **Sync → async is all-or-nothing.** The store fns are synchronous; Postgres makes them async, which is a type-level breaking change — do it in ONE pass and let `tsc` enumerate every caller. (Direct importers were few: app/api/interrogation/route.ts + lib/interrogationRunner.ts.)
- **Normalized + aggregate access = delete-and-reinsert children inside one transaction** on each save (or upsert). Simple + correct for load-whole/save-whole; revisit only if records grow large.
- **"DB-only" fights the chat agent's file context.** The agent reads <epic>.md via ultracodeRunner's --add-dir VAULT_DIR. Dropping the mirror means re-pointing it — cleanest is to keep regenerating <epic>.md as a READ-ONLY projection from the DB (DB stays source of truth) rather than going fully fileless.
- **Runtime prerequisite:** DB-only means the feature is dead until a reachable Postgres + DATABASE_URL exists. None on the dev machine — provision Docker postgres:16 first.

Schema + full plan live in the repo: db/schema.sql and doc/interrogation-persistence-plan.md.

## Related

- [[3 Resources/Work-Side/Vinnstack/Vinnstack auth providers two patterns and the rule for adding one]]

%% ai-graph-start %%

**Related notes:**
- [[Vinnstack interrogationStore full-aggregate rewrite loses concurrent updates to the same epic]]
- [[Batch multi-row INSERTs to cut round-trips on aggregate saves (Postgres)]]
- [[DB-first-with-file-fallback opt-in Postgres persistence over a file cache]]
- [[Per-key write lock for parallel aggregate writes; self-migrating column via idempotent ALTER]]
- [[Version artifacts by lifecycle event with content-dedupe, store in DB not files]]

**Relations:**
- Vinnstack Interrogation Room — *currently uses* — JSON files
- Vinnstack Interrogation Room — *will migrate to* — PostgreSQL
- lib/interrogationStore.ts — *manages* — Interrogation
- Interrogation — *comprises* — epic
- Interrogation — *comprises* — questions
- Interrogation — *comprises* — options
- Interrogation — *comprises* — answers
- Interrogation — *comprises* — visuals
- Interrogation — *comprises* — prd
- Interrogation — *comprises* — revisions
- Interrogation — *comprises* — stories
- Interrogation — *comprises* — flows
- JSON files — *are stored per* — epic
- Current persistence — *includes* — Markdown mirror
- Markdown mirror — *is read by* — chat agent
- Current persistence — *includes* — git auto-commit
- PostgreSQL — *will be* — fully normalized
- PostgreSQL — *will be* — DB-only
- lib/interrogationStore.ts functions — *are currently* — Sync
- PostgreSQL migration — *makes functions* — async
- async change — *is a* — type-level breaking change
- tsc — *identifies* — callers
- app/api/interrogation/route.ts — *calls* — lib/interrogationStore.ts functions
- lib/interrogationRunner.ts — *calls* — lib/interrogationStore.ts functions
- Normalized + aggregate access — *requires* — delete-and-reinsert children
- delete-and-reinsert children — *occurs in* — one transaction
- DB-only — *conflicts with* — chat agent's file context
- chat agent — *reads* — <epic>.md
- chat agent — *uses* — ultracodeRunner
- ultracodeRunner — *uses* — VAULT_DIR
- Solution for chat agent — *is to regenerate* — <epic>.md
- <epic>.md — *is a* — READ-ONLY projection
- DB — *is the* — source of truth
- DB-only — *requires* — reachable Postgres
- DB-only — *requires* — DATABASE_URL
- Prerequisite — *is to provision* — Docker postgres:16
- Schema — *is defined in* — db/schema.sql
- Full plan — *is documented in* — doc/interrogation-persistence-plan.md
- Current note — *is related to* — 3 Resources/Work-Side/Vinnstack/Vinnstack auth providers two patterns and the rule for adding one

%% ai-graph-end %%