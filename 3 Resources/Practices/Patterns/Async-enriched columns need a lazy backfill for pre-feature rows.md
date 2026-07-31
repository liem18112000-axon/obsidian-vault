---
ai_hash: 48e50cbdce1c869c
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-23
entities: []
source: session 2026-07-23
status: seedling
tags:
- vinnstack
- database
- llm
- backfill
- gotcha
title: Async-enriched columns need a lazy backfill for pre-feature rows
type: lesson
---

# Async-enriched columns need a lazy backfill for pre-feature rows

When a column is filled by a background job at **creation time** (e.g. an AI-written changelog generated when a new artifact version is snapshotted), every row created **before the feature shipped** stays NULL forever — and any optimistic UI placeholder ("summary is being written… refresh shortly") becomes a permanent lie for those rows, which reads as a bug.

Fix: **opportunistic lazy backfill on read.** When the list endpoint is fetched, fire-and-forget generation for rows still missing the value, with two guards:

- **cap per fetch** (e.g. 2) so one view doesn't spawn a burst of LLM runs;
- **once-per-id-per-process attempt set** so a *failing* generation can't turn every subsequent fetch into a fresh paid call.

Viewing the panel is what heals the data, so the placeholder text becomes true again — no migration script, no retro batch job. (Vinnstack: `backfillExplanations()` in `lib/interrogation/mdExport.ts`, called from `GET /api/export/md?epic&kind`.)

General rule: whenever you add write-time async enrichment, ask "what about the rows that already exist?" — either backfill lazily on read, or make the UI distinguish *pending* from *will never arrive*.

## Related

- [[Vinnstack versioned MD exports]]

%% ai-graph-start %%

**Related notes:**
- [[Per-key write lock for parallel aggregate writes; self-migrating column via idempotent ALTER]]
- [[Version changelogs - generate the explanation at snapshot time, compute the diff on demand]]
- [[Vinnstack story flows keep only the latest version - history lives in md_exports snapshots]]
- [[Vinnstack interrogationStore full-aggregate rewrite loses concurrent updates to the same epic]]
- [[A feedback loop with only its write side wired looks like a broken feature]]

%% ai-graph-end %%