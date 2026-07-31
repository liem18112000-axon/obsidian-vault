---
title: "mockStatic ConfigProvider without getConfig stub latches null into static Config fields"
created: 2026-07-22
type: lesson
status: seedling
source: "ParallelizeGateTest 2026-07-22"
tags: [java, mockito, microprofile, gotcha]
---

# mockStatic ConfigProvider without getConfig stub latches null into static Config fields

Forcing a class to initialise inside `mockStatic(ConfigProvider.class)` WITHOUT stubbing `getConfig()` makes the mocked static return null, so a `private static Config config = ConfigProvider.getConfig();` field latches null for the whole JVM — every later REAL call on that class NPEs, even after the MockedStatic scope closes (static init runs once). Seen in ParallelizeGateTest @BeforeAll warming up PropertyRetriever. If you must init container-bound statics under mockStatic, stub the factory: `cp.when(ConfigProvider::getConfig).thenReturn(mock(Config.class))`.

## Related

- [[Luz gates must inject per-package Allowlist beans not static Campaign isAffectedFor]]
