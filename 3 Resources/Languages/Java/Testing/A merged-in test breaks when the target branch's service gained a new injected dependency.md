---
title: "A merged-in test breaks when the target branch's service gained a new injected dependency"
created: 2026-06-04
type: gotcha
status: seedling
source: "session 2026-06-04, FolderServiceRecoverFolderTest merge"
tags: [testing, mockito, merge, injectmocks]
---

# A merged-in test breaks when the target branch's service gained a new injected dependency

When a test class merges cleanly into another branch (no textual conflict), it can still break at runtime if the class under test gained a new `@Inject` collaborator on that branch: `@InjectMocks` silently leaves un-mocked fields `null`, and the first code path touching the new dependency throws NPE.

Concrete case (luz_docs, 2026-06-04): `FolderServiceRecoverFolderTest` from the LUZ-155136 branch merged into the LUZ-155107 materialize branch, where `FolderService.recoverFolder` now calls `materializeFacade.shouldCascadeFolderRecovery(...)` in a `finally` block. Without `@Mock MaterializeFacade` the test would NPE after recovery. Fix: add the `@Mock` field â€” a default Mockito mock returns `false` from the boolean guard, so the cascade is skipped and the test stays focused.

**Checklist after resolving a merge that touches a service + its tests:** diff the service's `@Inject` fields against the test's `@Mock` fields; any field present in the service but missing in the test is a latent NPE (Mockito won't warn). Then run the tests â€” a clean textual merge is not a semantic merge.

## Related
- [[Folder recovery must recompute inherited security after deletion statuses are cleared]]
- [[3 Resources/Programming/Java/Testing/Interaction-style mocks hide ordering bugs that a stateful in-memory fake exposes]]

