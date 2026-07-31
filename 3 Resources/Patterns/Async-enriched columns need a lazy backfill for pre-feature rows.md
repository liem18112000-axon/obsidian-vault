---
title: "Async-enriched columns need a lazy backfill for pre-feature rows"
created: 2026-07-23
type: lesson
status: seedling
source: "session 2026-07-23"
tags: [vinnstack, database, llm, backfill, gotcha]
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
