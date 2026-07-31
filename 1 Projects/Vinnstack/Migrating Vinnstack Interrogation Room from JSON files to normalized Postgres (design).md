---
title: "Migrating Vinnstack Interrogation Room from JSON files to normalized Postgres (design)"
created: 2026-07-02
type: argument
status: seedling
source: "session 2026-07-02"
tags: [vinnstack, postgres, persistence, migration, interrogation-room]
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

- [[Vinnstack auth providers: two patterns and the rule for adding one]]
