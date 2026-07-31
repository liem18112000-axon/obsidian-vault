---
ai_hash: 6eebcd97812b6273
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-05
entities: []
source: session 2026-06-05 LUZ-154804
status: seedling
tags:
- mockito
- strict-stubs
- unit-testing
- gotcha
title: Mockito strict stubs flag mismatched-arg calls on a stubbed method as failures
type: gotcha
---

# Mockito strict stubs flag mismatched-arg calls on a stubbed method as failures

Under MockitoExtension's default STRICT_STUBS, invoking a mock method that HAS a registered stubbing but with NON-matching arguments raises PotentialStubbingProblem at invocation time. If the code under test wraps that call in catch(Exception), the Mockito error is silently swallowed and the call appears to have *failed for business reasons* — corrupting the very failure-handling path you are testing.

Symptom seen in luz_docs: `doThrow(...).when(repo).persist(eq(T), eq(token), eq("d2"), any())` made the d1 call ALSO land in the failed-ids list, because d1's mismatched invocation threw PotentialStubbingProblem into the service's per-document catch block. The test asserting failed == [d2] got [d1, d2]. The bug stays invisible as long as the test only asserts that *some* failure happened (assertThrows), and surfaces the moment you assert failure *content*.

Fix: stub the whole argument space once and branch inside:
```java
doAnswer(inv -> {
    if ("d2".equals(inv.getArgument(2))) throw new RuntimeException("boom");
    return null;
}).when(repo).persist(eq(T), eq(token), anyString(), any(JsonObject.class));
```

Related: [[luz_docs materialize passive retry via cascade markers]]

## Related

- [[luz_docs materialize passive retry via cascade markers]]

%% ai-graph-start %%

**Related notes:**
- [[Mockito strict stubbing turns removed production calls into UnnecessaryStubbingException test failures]]
- [[Mockito helper that stubs must not run inside an outer when().thenReturn() argument]]
- [[A merged-in test breaks when the target branch's service gained a new injected dependency]]
- [[MicroProfile Fallback is dead in plain Mockito unit tests]]
- [[Mockito @InjectMocks by type stale @Mock after @RestClient swap leaves real field null]]

%% ai-graph-end %%