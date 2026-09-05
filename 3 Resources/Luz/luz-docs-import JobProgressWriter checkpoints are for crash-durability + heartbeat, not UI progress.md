---
title: "luz-docs-import JobProgressWriter checkpoints are for crash-durability + heartbeat, not UI progress"
created: 2026-08-10
type: lesson
status: seedling
source: "session 2026-08-10 (import-job GET/DELETE usage findings)"
tags: [luz, luz-docs-import, idempotency, design-decision, mongodb]
---

# luz-docs-import JobProgressWriter checkpoints are for crash-durability + heartbeat, not UI progress

In luz-docs-import, `JobProgressWriter.flushThrottled()` periodically persists the in-flight import job to Mongo (`document-import-jobs`). It looks like a progress-for-UI mechanism but is NOT: the only consumer (luz_mylife_web dashboard `checkJobStatus`) polls `status` only and reacts solely to DONE/FAILED — intermediate `successfulFiles`/`unprocessedFiles` counts are never read by any consumer.

The checkpoints actually serve two server-side purposes:
1. **Idempotency durability** — `IdempotentImportService.getImportedFilePaths` reads prior jobs` persisted `successfulFiles` + `skippedFiles` (filtered by `importZipName`) to skip already-imported files on re-import. If the pod crashes mid-import with no checkpoint, the dead job persisted nothing → a re-import dedups nothing → DUPLICATE documents.
2. **Liveness heartbeat** — `JsonStoreService.updateJob` sets `lastModifield = Instant.now()`; `failJobIfStale` marks a non-terminal job FAILED(TIMEOUT) once `lastModifield` > 3600s old. Periodic writes stop a healthy long import from being wrongly timed out.

**Decision (2026-08-10):** collapsing to a single final write was considered (since no consumer reads progress) but rejected — it would break crash-safe dedup and risk false timeouts. Instead the cadence was COARSENED: `FLUSH_EVERY_N` 100→1000, `FLUSH_EVERY_MS` 1000→10000 — keeps durability + heartbeat, ~10x fewer Mongo writes on large imports. Related: [[Luz docs-import zip flow: upload-zip returns job-id, poll GET until DONE]], [[luz-docs-import bug: rejected files not removed from unprocessedFiles]].

## Related

- [[Luz docs-import zip flow: upload-zip returns job-id]]
- [[poll GET until DONE]]
- [[luz-docs-import bug: rejected files not removed from unprocessedFiles]]
