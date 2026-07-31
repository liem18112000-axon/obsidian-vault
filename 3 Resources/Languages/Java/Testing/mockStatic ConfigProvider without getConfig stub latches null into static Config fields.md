---
ai_hash: d0ff8aa544f7a985
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-22
entities: []
source: ParallelizeGateTest 2026-07-22
status: seedling
tags:
- java
- mockito
- microprofile
- gotcha
title: mockStatic ConfigProvider without getConfig stub latches null into static Config
  fields
type: lesson
---

# mockStatic ConfigProvider without getConfig stub latches null into static Config fields

Forcing a class to initialise inside `mockStatic(ConfigProvider.class)` WITHOUT stubbing `getConfig()` makes the mocked static return null, so a `private static Config config = ConfigProvider.getConfig();` field latches null for the whole JVM — every later REAL call on that class NPEs, even after the MockedStatic scope closes (static init runs once). Seen in ParallelizeGateTest @BeforeAll warming up PropertyRetriever. If you must init container-bound statics under mockStatic, stub the factory: `cp.when(ConfigProvider::getConfig).thenReturn(mock(Config.class))`.

## Related

- [[Luz gates must inject per-package Allowlist beans not static Campaign isAffectedFor]]

%% ai-graph-start %%

**Related notes:**
- [[Luz gates must inject per-package Allowlist beans not static Campaign isAffectedFor]]
- [[New collaborator call NPEs old @InjectMocks tests]]
- [[Mockito @InjectMocks by type stale @Mock after @RestClient swap leaves real field null]]
- [[Mockito helper that stubs must not run inside an outer when().thenReturn() argument]]
- [[JUnit5 @BeforeAll must be static - non-static masks every test in the class]]

%% ai-graph-end %%