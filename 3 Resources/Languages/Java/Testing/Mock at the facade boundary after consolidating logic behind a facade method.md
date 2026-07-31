---
title: "Mock at the facade boundary after consolidating logic behind a facade method"
created: 2026-06-01
type: lesson
status: seedling
source: "session 2026-06-01"
tags: [java, testing, mockito, refactoring, facade]
---

# Mock at the facade boundary after consolidating logic behind a facade method

After moving multi-step logic into a single facade method, rewrite the caller's tests to mock the facade boundary, not the internals the facade now hides.

**Example:** `JsonStoreMongoService.queryDocumentCollection` used to call `materializeFacade.buildListDocumentQueryFilter`, then `jsonStoreMongoClient.find`, then `materializeFacade.addFolderObjects` in sequence. After the refactor it just calls `materializeFacade.queryDocumentsMaterialized(...)`. The test went from mocking three things (`buildListDocumentQueryFilter` + `find` + `addFolderObjects`) to mocking one (`queryDocumentsMaterialized`).

**Why:** mocking internals after the consolidation:
- exposes the test to refactors that move pieces around inside the facade (test becomes brittle to private impl),
- duplicates the new public contract in test-setup form (two declarations of "what materialize does"),
- ends up testing the *old* call sequence rather than the *new* boundary.

**Rule of thumb:** when a facade method swallows a stable contract, the caller's test should match the new arity. The facade's own test (`MaterializeFacadeTest`) is where the internal steps still get exercised.

## Related
[[Parallel-API suffix to add behaviour without breaking existing callers]]
