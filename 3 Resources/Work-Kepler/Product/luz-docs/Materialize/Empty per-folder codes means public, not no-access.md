---
title: "Empty per-folder codes means public, not no-access"
created: 2026-06-01
type: lesson
status: seedling
source: "session 2026-06-01"
tags: [luz-docs, materialize, security, gotcha]
---

# Empty per-folder codes means public, not no-access

In `MaterializeResponseBuilder.isFolderVisible`, an entry in `_folderSecurityClassCodes` that is an **empty array** signals "this folder has no restrictions" → the folder stays visible regardless of the caller's user codes.

**The three branches:**
1. perFolderCodes is **not an ARRAY** (missing/malformed sentinel) → visible (fail-open).
2. perFolderCodes is an **empty array** → visible (open folder).
3. perFolderCodes has values → visible **only if** any element intersects userCodes; otherwise filtered out.

**Why fail-open on missing sentinel:** unmaterialized docs would otherwise vanish from response, hiding their existence rather than denying access — a worse failure mode than over-sharing during a partial backfill.

**Test-design note:** to unit-test the "open folder" branch, the doc must include `_folderSecurityClassCodes` with an empty inner array `[[]]`, not omit the field. Omitting hits branch 1 (fail-open), not branch 2 (genuine open folder) — different code path, equivalent outcome.

## Related
[[Parallel arrays in materialize sentinel preserve folderId order]]
