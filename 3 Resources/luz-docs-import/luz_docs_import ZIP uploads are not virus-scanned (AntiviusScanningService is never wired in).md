---
title: "luz_docs_import ZIP uploads are not virus-scanned (AntiviusScanningService is never wired in)"
created: 2026-07-31
type: lesson
status: seedling
source: "graphify investigation 2026-07-31, commit df874966"
tags: [luz-docs-import, security, gotcha, dead-code, graphify]
---

# luz_docs_import ZIP uploads are not virus-scanned (AntiviusScanningService is never wired in)

In `luz_docs_import` (verified 2026-07-31, commit df874966) the whole antivirus mechanism is coded but **dead**: `AntiviusScanningService.scanUploadFile()` calls `luz_antivirus` via `AntivirusRestClient`, and `FailureCode.INFECTED`, `JobStatus.SCANNING`, and `BundleConstant.SCANNING_VIRUS_FAIL` all exist — yet the service has **no `@Inject` site and no caller anywhere**. So uploaded ZIPs currently pass through the import flow **without ever being virus-scanned**.

`JobStatus.SCANNING` is declared but never assigned, which is consistent — the state the scan step would have set is unreachable.

**Why it matters / how to apply:** the scaffolding implies scanning was meant to sit between zip validation and job creation (or before each `createDocument` call in the async worker) and was never connected. Before assuming "the importer scans for malware", grep for a caller — there isnt one. If this is a security requirement, wiring it is a real gap, not a config toggle. Confirm with the team whether the omission is intentional.

How it was found: graphify code graph (node present, zero inbound call edges) + `grep -rn "AntiviusScanningService" src | grep -i inject` returning nothing.

See the full flow in the repo report `docs/document-import-technical-path.md` (§7 finding #1). Related: [[luz_docs_import detects dead async-worker pods via kubectl during status polling]]

## Related

- [[luz_docs_import detects dead async-worker pods via kubectl during status polling]]
