---
title: "Fail-closed defense over a parallel array: distinguish present-but-short from absent"
created: 2026-06-10
type: lesson
status: seedling
source: "luz_docs N2 fix, session 2026-06-10"
tags: [fail-closed, security, luz-docs, materialize, gotcha, denormalization]
---

# Fail-closed defense over a parallel array: distinguish present-but-short from absent

When you add a **fail-closed** read-path defense over a denormalized *parallel array* (a sentinel array meant to be index-aligned with another — e.g. `_folderSecurityClassCodes` aligned to `folderIds`), do not treat every missing slot the same. Distinguish three states of the array, because they mean different things:

- **Present + aligned** → enforce normally.
- **Present but SHORT** (size < the array it should mirror) → a real desync; fail **closed** (hide / deny the slot-less positions). This is the genuine attack/leak surface.
- **ABSENT entirely** → often a *legacy/partially-materialized* record, not an attack. Failing closed here can hide everything and cause a functional regression.

**Why the distinction is load-bearing:** the completeness gate that decides whether a doc counts as "materialized" may check fewer fields than the read path relies on. In luz_docs, `buildUnmaterializedFilter` checks only 3 of the 4 sentinel fields and **omits** `_folderSecurityClassCodes`. So older 3-sentinel docs pass the gate as "materialized" yet lack that field. A blanket `i >= size -> hide` would blank out all folders on those docs.

**The targeted predicate** (read-path defense for finding N2):
```java
boolean hasCodeSlots = doc.containsKey(FOLDER_SECURITY_CLASS_CODES);
.filter(i -> i < folderCodes.size()
        ? isFolderVisible(folderCodes.get(i), userCodes)  // present slot: enforce
        : !hasCodeSlots)                                  // missing slot: hide iff array present-but-short
```
A present-but-**empty** slot (`[]`) still legitimately means a *public* folder and stays visible — empty != missing.

**General lesson:** a security defense and the freshness/completeness gate guarding the same data can skew on *which fields* they trust. Before failing closed on missing data, ask whether the gate even guarantees that field's presence; if not, scope the fail-closed to the provably-corrupt case (present-but-short) and leave the legacy-absent case on its prior path. Root-cause parity (don't let the array go short in the first place — e.g. dup-folderId collapse, #18) is the durable fix; the read-path check is defense in depth.

Context: luz_docs eArchive materialize, branch kepler/sprint-156/earchive-master, `MaterializeResponseBuilder.addFoldersFieldWithCodes`. Verified by 37 green `MaterializeResponseBuilderTest` cases with zero test changes (the targeted scope avoided breaking legacy-doc expectations).

## Related

- [[luz-docs eArchive materialize]]
