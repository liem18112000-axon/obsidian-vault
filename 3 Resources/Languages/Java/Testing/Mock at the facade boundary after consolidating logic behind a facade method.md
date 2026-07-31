---
ai_hash: ad6bc4b605154938
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-01
entities: []
source: session 2026-06-01
status: seedling
tags:
- java
- testing
- mockito
- refactoring
- facade
title: Mock at the facade boundary after consolidating logic behind a facade method
type: lesson
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

%% ai-graph-start %%

**Related notes:**
- [[Shape-keyed test mocks break when production query shapes change]]
- [[New collaborator call NPEs old @InjectMocks tests]]
- [[A delegating overload changes less code than widening an existing method signature]]
- [[Visibility downgrade breaks external callers]]
- [[Parameterize JUnit5 tests across overload variants with Named Function MethodSource]]

%% ai-graph-end %%