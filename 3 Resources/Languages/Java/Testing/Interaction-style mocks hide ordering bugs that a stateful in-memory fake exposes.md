---
title: "Interaction-style mocks hide ordering bugs that a stateful in-memory fake exposes"
created: 2026-06-04
type: lesson
status: seedling
source: "session 2026-06-04, FolderServiceRecoverFolderTest"
tags: [testing, mockito, fakes, ordering-bugs]
---

# Interaction-style mocks hide ordering bugs that a stateful in-memory fake exposes

When a bug is about **operation ordering against mutable state** (e.g. luz_docs LUZ-155136: a security recompute running before soft-delete flags were cleared), interaction-style Mockito stubs cannot catch it: a stub like `when(getCollectionMetadataByTerms(...)).thenReturn(parentFolder)` answers correctly no matter when it is called, so the test passes even though the real query would have excluded the still-deleted parent.

The fix at test level: back the mocks with a small **stateful in-memory fake** (a `Map<String, JsonObject>` acting as the folder collection) where reads honor the deletion filter and writes (`updateFolderMetadata`, `updatePatchMetadata`) mutate the map. Then the test asserts **end-state** ('A, B, C all have inheritedSecurityClassCode=[SEC_1] and deletionStatus=false') instead of call counts — and an early-running recompute genuinely fails the test, exactly like production.

**Heuristic:** if the bug class is 'right call, wrong time' or 'read saw stale state', test with a fake that models the state machine; reserve pure interaction verification for protocol-only concerns (was X called with Y).

## Related
- [[Folder recovery must recompute inherited security after deletion statuses are cleared]]
