---
title: "Version artifacts by lifecycle event with content-dedupe, store in DB not files"
created: 2026-07-22
type: model
status: seedling
source: "session 2026-07-22"
tags: [versioning, export, postgres, vinnstack]
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
