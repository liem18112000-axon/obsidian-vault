---
title: "Luz _folderSecurityClassCodes is a list-of-lists, one inner list per folder"
created: 2026-06-08
type: concept
status: seedling
source: "session 2026-06-08 LUZ-154157"
tags: [luz-docs, materialize, security-class, gotcha]
---

# Luz _folderSecurityClassCodes is a list-of-lists, one inner list per folder

The Luz docs document materialized sentinel field `_folderSecurityClassCodes` (plural) is a **list of lists**, not a flat list. It holds one inner list per folder in the document's `folderIds` (loosely in folderIds order); each inner list is the union of that folder's `securityClassCodes` and `inheritedSecurityClassCodes`.

Key points:
- A **public folder** (no codes) still contributes an entry — an empty inner list `[]`.
- Duplicates are **not** collapsed across folders.

Examples:
- doc in a single public folder → `[[]]`
- doc in two folders that each carry `SC2_2` → `[["SC2_2"], ["SC2_2"]]`

Contrast with the sibling sentinel `_effectiveSecurityClassCodes`, which is a **flat, deduped set** of all codes across the doc's folders (plus the doc's own codes). The other materialize sentinels: `_isPublic` (bool) and `_folderNames` (flat list).

Discovered while writing folder-delete materialize cascade tests (LUZ-154157) in luz_docs_integration_test.

## Related
[[Comparing _folderSecurityClassCodes in tests needs a multiset-of-sets, not a flat set]]

## Related

- [[Comparing _folderSecurityClassCodes in tests needs a multiset-of-sets]]
- [[not a flat set]]
