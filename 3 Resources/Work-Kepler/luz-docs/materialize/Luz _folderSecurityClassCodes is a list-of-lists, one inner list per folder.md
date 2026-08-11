---
ai_hash: cfdd7cb7988933b8
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-08
entities: []
source: session 2026-06-08 LUZ-154157
status: seedling
tags:
- luz-docs
- materialize
- security-class
- gotcha
title: Luz _folderSecurityClassCodes is a list-of-lists, one inner list per folder
type: concept
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

- [[3 Resources/Work-Kepler/luz-docs/materialize/Comparing _folderSecurityClassCodes in tests needs a multiset-of-sets, not a flat set]]

%% ai-graph-start %%

**Related notes:**
- [[Comparing _folderSecurityClassCodes in tests needs a multiset-of-sets, not a flat set]]
- [[Luz delete-folder tests can only delete public folders, not ones carrying a security class]]
- [[luz_docs parent-change cascade pipeline rebuilds _folderSecurityClassCodes positionally then re-derives the sentinels]]
- [[securityClassCodes scalar string breaks materialize sentinels]]
- [[luz_docs folder security-class changes have 3 entry points but only PUT cascades]]

%% ai-graph-end %%