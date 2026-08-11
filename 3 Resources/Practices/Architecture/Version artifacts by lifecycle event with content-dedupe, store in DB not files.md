---
ai_hash: a17687cc79062c9d
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-22
entities: []
source: session 2026-07-22
status: seedling
tags:
- versioning
- export
- postgres
- vinnstack
title: Version artifacts by lifecycle event with content-dedupe, store in DB not files
type: model
---

# Version artifacts by lifecycle event with content-dedupe, store in DB not files

Design for exporting generated artifacts (interrogations, PRD, stories) as versioned MD in Vinnstack: a version is minted only at **lifecycle events** (submit / regenerate / manual export), and consecutive identical content is **deduped** by comparing against the latest stored version — so no-op exports never mint versions, and the version list reads as a true change history.

Why it works:
- Lifecycle-event versioning matches how humans think about artifact history ("the one I submitted" vs "after the regenerate"), unlike save-every-download.
- Content-dedupe makes the snapshot call idempotent, so it can be wired best-effort (`void trySnapshot()`) into every submit/regenerate route without version spam.
- Storing the MD **content in Postgres** (not files on disk) keeps the packaged Electron app self-contained — the "artifact link" is just an API route (`/api/export/md?id=N`) serving `Content-Disposition: attachment`.
- Wire snapshots at the **route layer**, not the store layer, to avoid circular imports (the export lib reads from the store; the store must never import the export lib).

## Related

- [[Kepler index]]

%% ai-graph-start %%

**Related notes:**
- [[Version changelogs - generate the explanation at snapshot time, compute the diff on demand]]
- [[Migrating Vinnstack Interrogation Room from JSON files to normalized Postgres (design)]]
- [[PRD-parity checklist - what comment-driven regenerate with versions actually requires]]
- [[Vinnstack story flows keep only the latest version - history lives in md_exports snapshots]]
- [[Async-enriched columns need a lazy backfill for pre-feature rows]]

%% ai-graph-end %%