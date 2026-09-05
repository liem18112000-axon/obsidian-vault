---
title: "luz-docs-import dedup identity is the uploaded zip filename (importZipName)"
created: 2026-08-11
type: lesson
status: seedling
source: "session 2026-08-11, gap3_test.sh"
tags: [luz-docs-import, dedup, api, testing, gotcha]
---

# luz-docs-import dedup identity is the uploaded zip filename (importZipName)

On `POST /luz_docs_import/api/{tenant}/import-jobs/upload-zip`, the server sets `importZipName = FilenameUtils.removeExtension(uploadedMultipartFilename)` (`DocsImportService.importZipFile` → `createTempFile` → `FileMultipartUtil.getFileName`). The idempotency/dedup skip-set is keyed on **`(importZipName, relativePath)`** (`IdempotentImportService`), where `relativePath` is NFC-normalized after the Gap-3 fix (`ImportJob.getPathFile`).

**Consequence for testing the NFC/NFD dedup-convergence case:** two different archives are only compared against each other if they upload under the **same filename**. So `gap3-nfc.zip` and `gap3-nfd.zip` will NOT dedup against each other as-is (importZipName `gap3-nfc` vs `gap3-nfd`). You must upload both under one shared name, e.g. curl `--form "file=@/path/gap3-nfd.zip;filename=gap3-unicode.zip"`. Only then does the NFD import skip everything the NFC import created — proving the keys converged.

**Discriminator for a broken vs fixed normalization:** after importing NFC under name X, importing NFD under the same name X must yield `successfulFiles`=0 and every doc in `skippedFiles`. If the NFC-normalization were missing, the NFD paths wouldn't match the stored NFC keys and would import as 100 NEW docs (successfulFiles=100) instead of skipping.

**Verifier-oracle trick:** to check the job result without hardcoding expected names, read the expected doc paths straight from the ZIP's own entry list, NFC-normalize both the zip paths and the job's `successfulFiles[].filePath`/`skippedFiles[].filePath`, and assert coverage. A mojibake decode (pre-fix, CP437 misread of UTF-8) shows up as uncovered paths like `Hß╗ô s╞í y tß║┐/R├╢ntgenbild ...`. Implemented in `~/.claude/skills/luz-docs-import-api-test/_lib/verify_gap3.py`; orchestrated by `gap3_test.sh` (uses `import_test.sh`'s `UPLOAD_NAME` + `RESULT_FILE` hooks).

## Related

- [[luz-docs-import ingest ZIP format (folders + per-file .metadata.json sidecar)]]
- [[Building a ZIP fixture to test NFC/NFD + UTF-8-flag entry-name handling]]
