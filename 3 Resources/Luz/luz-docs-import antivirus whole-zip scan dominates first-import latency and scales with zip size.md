---
title: "luz-docs-import: antivirus whole-zip scan dominates first-import latency and scales with zip size"
created: 2026-08-10
type: observation
status: seedling
source: "session 2026-08-10 (lam_transfer.zip test)"
tags: [luz, luz-docs-import, antivirus, performance, latency]
---

# luz-docs-import: antivirus whole-zip scan dominates first-import latency and scales with zip size

In luz-docs-import, the FIRST step of the import flow is a whole-zip antivirus scan (`AntiviusScanningService.scanUploadFile`, called before unzip/dedup in `DocsImportAsyncService.importZipAndCleanFile`). Its latency scales with archive size and is the single largest contributor to first-import wall-clock:

- 9 KB `transfer.zip` → `/scanner` call ≈ 0.7 s
- 3.3 MB `lam_transfer.zip` → `/scanner` call ≈ 42 s (job sat in `UPLOADED`/`SCANNING` ~28 s before `DOCUMENT_CREATING`)

Measured as luz-docs-import`s outbound `time-consuming=` on `…/luz_antivirus/api/scanner` (see [[Trace Luz per-service latency via the time-consuming= log marker]]).

**Observation (unconfirmed):** re-uploading the byte-identical zip (dedup run) showed NO comparable ~40 s scanner cost — luz-docs-import made only ~24 short calls. Antivirus may short-circuit/cache identical content. The dedup skip itself happens AFTER the scan (idempotency is checked per-file post-unzip), so a re-import is not free of scan cost in general. Related: [[Luz docs-import zip flow: upload-zip returns job-id, poll GET until DONE]].

## Related

- [[Trace Luz per-service latency via the time-consuming= log marker]]
- [[Luz docs-import zip flow: upload-zip returns job-id]]
- [[poll GET until DONE]]
