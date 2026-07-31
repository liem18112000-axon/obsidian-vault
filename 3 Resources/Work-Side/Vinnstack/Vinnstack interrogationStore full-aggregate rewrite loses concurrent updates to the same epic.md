---
ai_hash: f70d03ebe062c3b2
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-07
entities: []
source: Vinnstack session 2026-07-07
status: seedling
tags:
- vinnstack
- postgres
- concurrency
- race-condition
- gotcha
title: Vinnstack interrogationStore full-aggregate rewrite loses concurrent updates
  to the same epic
type: lesson
---

# Vinnstack interrogationStore full-aggregate rewrite loses concurrent updates to the same epic

In Vinnstack's `lib/interrogationStore.ts`, `saveInterrogation()` persists an epic's interrogation state (questions/PRD/stories) by deleting all of that epic's child rows and reinserting them fresh, inside one transaction — a full-aggregate rewrite rather than a targeted update.

The gotcha: if two `saveInterrogation()` calls for the *same epic* run concurrently (e.g. two in-flight generation requests racing), the second transaction's delete+reinsert can silently overwrite/lose the first one's writes — a classic lost-update race, made worse here because the "update" is actually delete-then-insert rather than a row-level UPDATE. This is documented directly in the file (`lib/interrogationStore.ts:552-562`).

The fix already applied for one code path, `approveStoryFlow`, was to bypass the aggregate rewrite entirely and issue a targeted single-row `UPDATE` instead — the safe pattern to replicate for any new per-item write against this store (e.g. a future per-story process-flow queue) rather than reusing the full-aggregate `saveInterrogation()`.

Relevant because any queueing feature added on top of the Interrogation Room or Process Flow (concurrent generation of multiple items) must either serialize writes per-epic or route through a targeted row update — not the aggregate rewrite — to avoid resurrecting this race. See [[Promise-chain queueTail pattern serializes async jobs with instant enqueue]] for the serialization half of that fix.

## Related

- [[Promise-chain queueTail pattern serializes async jobs with instant enqueue]]

%% ai-graph-start %%

**Related notes:**
- [[Per-key write lock for parallel aggregate writes; self-migrating column via idempotent ALTER]]
- [[Migrating Vinnstack Interrogation Room from JSON files to normalized Postgres (design)]]
- [[Delete-and-reinsert aggregate saves silently cascade-wipe new child tables]]
- [[Batch multi-row INSERTs to cut round-trips on aggregate saves (Postgres)]]
- [[Whole-aggregate read-modify-write for a per-child toggle causes lost updates under concurrent sibling writes]]

%% ai-graph-end %%