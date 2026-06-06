---
title: "Mockito helper that stubs must not run inside an outer when().thenReturn() argument"
created: 2026-06-05
type: lesson
status: seedling
source: "session 2026-06-05 luz_docs change-tracking"
tags: [mockito, java, testing, gotcha]
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
