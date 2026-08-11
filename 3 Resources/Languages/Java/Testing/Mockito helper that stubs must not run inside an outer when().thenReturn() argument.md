---
ai_hash: d2ef8f05c126d86e
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-05
entities: []
source: session 2026-06-05 luz_docs change-tracking
status: seedling
tags:
- mockito
- java
- testing
- gotcha
title: Mockito helper that stubs must not run inside an outer when().thenReturn()
  argument
type: lesson
---

# Mockito helper that stubs must not run inside an outer when().thenReturn() argument

Calling a test helper that performs its own stubbing (e.g. `lenient().when(response.getStatus()).thenReturn(...)`) from inside the argument list of an outer `when(mock.call(...)).thenReturn(helper(...))` throws `UnfinishedStubbing`. Mockito tracks one stubbing-in-progress at a time: the outer `when(...)` is still open when the helper starts a nested stub, corrupting the state.

Fix: pre-create the stubbed mock into a local variable first, then pass the variable.

```java
// broken
when(client.updateOne(...)).thenReturn(response(200, "{}"));
// fixed
var ok = response(200, "{}");
when(client.updateOne(...)).thenReturn(ok);
```

Bit me in TrackingJsonStoreClientTest (luz_docs) — 8 errors at once, all the same root cause.

%% ai-graph-start %%

**Related notes:**
- [[Mockito strict stubs flag mismatched-arg calls on a stubbed method as failures]]
- [[Mockito strict stubbing turns removed production calls into UnnecessaryStubbingException test failures]]
- [[Mockito @InjectMocks by type stale @Mock after @RestClient swap leaves real field null]]
- [[Interaction-style mocks hide ordering bugs that a stateful in-memory fake exposes]]
- [[New collaborator call NPEs old @InjectMocks tests]]

%% ai-graph-end %%