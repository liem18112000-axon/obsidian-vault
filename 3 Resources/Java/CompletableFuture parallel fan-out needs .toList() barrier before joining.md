---
title: "CompletableFuture parallel fan-out needs .toList() barrier before joining"
created: 2026-06-16
type: lesson
status: seedling
source: "LUZ-154613 session 2026-06-16"
tags: [java, concurrency, completablefuture, streams, gotcha]
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

- [[Parallelize visible-doc count by fan-out over _id ranges (luz-docs)]]
