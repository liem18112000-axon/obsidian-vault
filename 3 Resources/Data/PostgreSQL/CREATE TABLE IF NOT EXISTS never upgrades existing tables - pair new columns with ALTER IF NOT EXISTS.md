---
ai_hash: eb4124c9f7bd3573
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-03
entities: []
source: session 2026-07-03, vinnstack subject column
status: seedling
tags:
- postgres
- schema-migration
- ddl
title: CREATE TABLE IF NOT EXISTS never upgrades existing tables - pair new columns
  with ALTER IF NOT EXISTS
type: lesson
---

# CREATE TABLE IF NOT EXISTS never upgrades existing tables - pair new columns with ALTER IF NOT EXISTS

When a project's whole schema lives in one idempotent `schema.sql` full of `CREATE TABLE IF NOT EXISTS`, adding a column to the CREATE statement silently does NOTHING for databases where the table already exists - IF NOT EXISTS skips the entire statement, so fresh installs get the column and existing installs don't, and the drift only surfaces as runtime `column does not exist` errors.

Rule: every column added after a table first shipped needs a paired, equally idempotent upgrade line right next to the CREATE:

    ALTER TABLE t ADD COLUMN IF NOT EXISTS subject TEXT NOT NULL DEFAULT 'prd';

Both statements stay in schema.sql forever; re-running the file is then a no-op on any database state. Same applies to `CREATE INDEX IF NOT EXISTS` for new indexes (those are standalone statements, so they already behave).

%% ai-graph-start %%

**Related notes:**
- [[Per-feature migration scripts leave new tables silently missing until run]]
- [[Idempotency guards keyed on object presence break when hydration materializes the object]]

%% ai-graph-end %%