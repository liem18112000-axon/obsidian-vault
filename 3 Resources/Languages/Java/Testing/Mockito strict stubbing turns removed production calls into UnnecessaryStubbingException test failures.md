---
title: "Mockito strict stubbing turns removed production calls into UnnecessaryStubbingException test failures"
created: 2026-06-11
type: lesson
status: seedling
source: "luz_docs_statistic test fix, session 2026-06-11"
tags: [mockito, junit5, testing, gotcha]
---

# Mockito strict stubbing turns removed production calls into UnnecessaryStubbingException test failures

With MockitoExtension's default STRICT_STUBS, a refactor that removes a call from production code (e.g. dropping a redundant `response.getStatus()` validation) makes every test stub of that call fail with `UnnecessaryStubbingException` — the whole test class errors even though nothing it asserts changed.

Gotcha encountered in luz_docs_statistic: commit d9567a3 removed response-status checks from MongoDBService but left `Mockito.when(response.getStatus())...` stubs in MongoDBServiceTest, silently breaking the suite for the next person (me). Fix is to delete the orphaned stubs, not to blanket-apply `lenient()`.

Lesson: when you remove a call from the code under test, grep the tests for stubs of that call in the same commit.
