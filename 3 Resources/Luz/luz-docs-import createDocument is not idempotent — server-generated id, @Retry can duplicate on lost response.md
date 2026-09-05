---
title: "luz-docs-import createDocument is not idempotent — server-generated id, @Retry can duplicate on lost response"
created: 2026-08-10
type: observation
status: seedling
source: "session 2026-08-10 (createDocument idempotency check)"
tags: [luz, luz-docs-import, idempotency, duplication, retry, gotcha]
---

# luz-docs-import createDocument is not idempotent — server-generated id, @Retry can duplicate on lost response

luz-docs-import `LuzDocsViewControllerService.createDocument` sends `POST /documents` with only `files` + `folderIds` + `metadata` (origin/dates/title/health-data via `HealthDocImporter.applyMetadata`). **No client-supplied id / idempotency key** — the document id is minted server-side (downstream in luz-docs-view-controller/luz-docs) per POST. So creation is NOT idempotent.

**Two duplication vectors:**
1. **`@Retry(delay=3000)` on createDocument** — if the POST succeeds at luz-docs but the RESPONSE is lost (timeout/5xx on return), the retry creates a SECOND document for the same file. Happens within a single import; no job-doc dedup can prevent it.
2. **Re-import** — dedup rests ENTIRELY on the app-level `IdempotentImportService` (reads prior jobs` `successfulFiles`); if that record is lost, re-import duplicates.

**Making it idempotent (Tier 3) is a cross-service contract change, not luz-docs-import-only:** client computes a deterministic key (e.g. `hash(tenantId+importZipName+relativePath[+contentHash])`) and sends it; the downstream `POST /documents` endpoint must ACCEPT it and treat it as an UPSERT key (create-if-absent). Only this closes BOTH vectors. Related: [[A durable queue fixes report-write durability, not data-duplication — make the side effect idempotent]], [[luz-docs-import JobProgressWriter checkpoints are for crash-durability + heartbeat, not UI progress]].

## Related

- [[A durable queue fixes report-write durability]]
- [[not data-duplication — make the side effect idempotent]]
- [[luz-docs-import JobProgressWriter checkpoints are for crash-durability + heartbeat]]
- [[not UI progress]]
