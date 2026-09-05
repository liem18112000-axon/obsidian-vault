---
title: "Verify invariants at the source of truth, not the operation's success report"
created: 2026-08-19
type: lesson
status: seedling
source: "luz-docs-import v2+v3 matrix run 2026-08-19"
tags: [testing, verification, gotcha, data-integrity]
---

# Verify invariants at the source of truth, not the operation's success report

An operation that reports success can still have produced a partially-wrong result, so validate the real invariant against the **source of truth** (the database / persisted state), not the operation's own summary object.

**Concrete case.** A luz-docs-import ZIP job listed documents in `successfulFiles` with `failedFolders: []` — looking only at the job document, everything passed. But the documents whose (dot-prefixed) parent folder was silently dropped had actually been created at the archive **root**. The only way to prove it was querying MongoDB directly: `documents.folderIds == []` means root-orphaned. The job JSON kept the zip-relative `filePath` and never revealed the discrepancy.

**Why it happens.** A summary/status field is written by the same code path whose bug you are testing for; it encodes what the code *believes* it did, not what landed in storage. When the invariant under test is about final state ("no document is ever at the root"), assert on final state.

**Rule of thumb.** For any "X never happens" invariant, find the field in the data store that would be non-conforming if X happened, and query it — treat a green summary as a hint, not proof.

## Related

- [[Confirm the deployed artifact contains the fix before judging an env test]]
