---
ai_hash: 4cf83c9b813485aa
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-19
entities: []
source: Vinnstack session 2026-07-19
status: seedling
tags:
- concurrency
- postgres
- migration
- vinnstack
- store-pattern
title: Per-key write lock for parallel aggregate writes; self-migrating column via
  idempotent ALTER
type: lesson
---

# Per-key write lock for parallel aggregate writes; self-migrating column via idempotent ALTER

Two store patterns from adding parallel process-flow generation + a new column to Vinnstack (2026-07):

**1. Per-key write lock for aggregate stores.** `setStoryFlow` (and every writer in interrogationStore) does getInterrogation -> mutate in memory -> saveInterrogation, and saveInterrogation DELETE+reinserts the whole epic aggregate (questions/prds/stories). So two concurrent writes to the SAME epic each read a stale snapshot and the last save clobbers the other = lost update. "Generate ALL flows in parallel" fires many setStoryFlow for one epic, hitting exactly this. Fix without refactoring persistence: a per-epic in-memory promise-chain mutex — `withEpicLock(epic, fn)` chains each call after the previous settles, keyed by epic so different epics never block. The slow LLM runs still overlap; only the fast save is serialized. Works because the in-process Next server is the single writer. General rule: any read-modify-write of a whole aggregate is unsafe under concurrency — serialize per aggregate key or switch to targeted column upserts.

**2. Self-migrating column.** Vinnstack applies db/schema.sql only via manual migrate scripts, so adding a column to a live DB normally needs an ops step. To avoid that: a memoized `ensureSchemaExtensions()` in the store runs `ALTER TABLE ... ADD COLUMN IF NOT EXISTS ...` once per process (idempotent), awaited at the top of getInterrogation/saveInterrogation. The column also goes in schema.sql (CREATE TABLE + the same idempotent ALTER) for fresh DBs. Net: an existing DB gains the column on first access, no separate migration run.

Related: [[2 Areas/Vinnstack/Vinnstack release push to main triggers Cloud Build which publishes to GCS latest auto-update channel]].

%% ai-graph-start %%

**Related notes:**
- [[Vinnstack interrogationStore full-aggregate rewrite loses concurrent updates to the same epic]]
- [[Migrating Vinnstack Interrogation Room from JSON files to normalized Postgres (design)]]
- [[Async-enriched columns need a lazy backfill for pre-feature rows]]
- [[Batch multi-row INSERTs to cut round-trips on aggregate saves (Postgres)]]
- [[Vinnstack story flows keep only the latest version - history lives in md_exports snapshots]]

%% ai-graph-end %%