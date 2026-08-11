---
ai_hash: 5a3404b096e74786
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-16
entities: []
source: LUZ-154613 session 2026-06-16
status: seedling
tags:
- java
- concurrency
- completablefuture
- streams
- gotcha
title: CompletableFuture parallel fan-out needs .toList() barrier before joining
type: lesson
---

# CompletableFuture parallel fan-out needs .toList() barrier before joining

When fanning out N tasks with CompletableFuture over a single Java Stream, you MUST materialize the futures (e.g. `.toList()`) BEFORE the stream that calls `join()`. Streams are lazy and single-pass: a fused pipeline like

```java
items.stream()
     .map(x -> CompletableFuture.supplyAsync(() -> work(x), executor))
     .mapToInt(CompletableFuture::join)   // BUG: serial
     .sum();
```

processes one element end-to-end at a time → it submits task 1, immediately joins (blocks) task 1, then submits task 2… = fully serial, zero parallelism. Insert a barrier so all tasks are submitted first:

```java
items.stream()
     .map(x -> CompletableFuture.supplyAsync(() -> work(x), executor))
     .toList()        // <-- all N tasks now running concurrently
     .stream()
     .mapToInt(CompletableFuture::join)
     .sum();
```

Fail-loud is preserved without an explicit `CompletableFuture.allOf(...).join()`: each `join()` blocks and rethrows the first failure as `CompletionException` (catch + wrap). allOf is only needed when you want all to finish before inspecting, or to aggregate results via non-throwing `join` later. Used in luz-docs ParallelizeCount fan-out count.

## Related

- [[1 Projects/luz-docs/count/optimize/Divide-and-Conquer Visible-Document Count]]

%% ai-graph-start %%

**Related notes:**
- [[ManagedExecutorService.execute loses CDI request context]]
- [[Semaphore permit leak when risky code sits between acquire and try]]
- [[Widening fan-out threads doesn't help once MongoDB is the count bottleneck]]
- [[Per-pod single-flight kills cache stampede without semantic change]]
- [[Semaphore acquire before try leaks permits on static semaphores]]

%% ai-graph-end %%