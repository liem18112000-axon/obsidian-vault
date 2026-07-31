---
ai_hash: bcf1c7a14e24b67a
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-11
entities: []
source: luz_docs_statistic test fix, session 2026-06-11
status: seedling
tags:
- mockito
- junit5
- testing
- gotcha
title: Mockito strict stubbing turns removed production calls into UnnecessaryStubbingException
  test failures
type: lesson
---

# Mockito strict stubbing turns removed production calls into UnnecessaryStubbingException test failures

With MockitoExtension's default STRICT_STUBS, a refactor that removes a call from production code (e.g. dropping a redundant `response.getStatus()` validation) makes every test stub of that call fail with `UnnecessaryStubbingException` — the whole test class errors even though nothing it asserts changed.

Gotcha encountered in luz_docs_statistic: commit d9567a3 removed response-status checks from MongoDBService but left `Mockito.when(response.getStatus())...` stubs in MongoDBServiceTest, silently breaking the suite for the next person (me). Fix is to delete the orphaned stubs, not to blanket-apply `lenient()`.

Lesson: when you remove a call from the code under test, grep the tests for stubs of that call in the same commit.

%% ai-graph-start %%

**Related notes:**
- [[Mockito strict stubs flag mismatched-arg calls on a stubbed method as failures]]
- [[Mockito helper that stubs must not run inside an outer when().thenReturn() argument]]
- [[Mockito @InjectMocks by type stale @Mock after @RestClient swap leaves real field null]]
- [[New collaborator call NPEs old @InjectMocks tests]]
- [[A merged-in test breaks when the target branch's service gained a new injected dependency]]

%% ai-graph-end %%