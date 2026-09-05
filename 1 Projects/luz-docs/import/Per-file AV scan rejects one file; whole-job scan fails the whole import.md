---
title: "Per-file AV scan rejects one file; whole-job scan fails the whole import"
created: 2026-08-10
type: lesson
status: seedling
source: "session 2026-08-10, LUZ-158230"
tags: [luz-docs-import, antivirus, earchive, LUZ-158230, fail-closed, gotcha]
---

# Per-file AV scan rejects one file; whole-job scan fails the whole import

The two antivirus entry points in `AntiviusScanningService` differ deliberately in their failure semantics — this is the key design distinction:

- `scanUploadFile(...)` (the old whole-zip / whole-job scan) **throws** `DocsImportBackgroundException` (`INFECTED` on NOT_OK, `INTERNAL_SERVICE_ERROR` on any exception). A throw here fails the **entire import job**.
- `isFileClean(file, token)` (the new per-file scan) **swallows** timeouts and transport errors and returns `false`. It returns `true` **only** on an explicit `ScanningResult.OK`. So NOT_OK, a timeout, or any error all mean "reject this one file."

Effect: a bad per-file scan rejects only that single document — added to `job.rejectedFiles` with detail `"Virus found in the metadata file"` via `JobProgressWriter.recordRejected(file, detail)` — and the rest of the import continues. This is the whole point of moving from job-level to file-level scanning.

Requirement it satisfies (LUZ-158230): "if the antivirus times out OR the scan is abnormal => reject the file." Treating timeout the same as an abnormal result is intentional — fail closed.

Related: [[luz-docs-import scans metadata sidecars per-file, not the whole ZIP]], [[Metadata sidecars must be scanned in luz-docs-import because they are never uploaded]].

## Related

- [[luz-docs-import scans metadata sidecars per-file]]
- [[not the whole ZIP]]
- [[Metadata sidecars must be scanned in luz-docs-import because they are never uploaded]]
