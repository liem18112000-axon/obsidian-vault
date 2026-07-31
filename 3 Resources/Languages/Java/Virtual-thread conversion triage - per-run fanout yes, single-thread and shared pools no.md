---
ai_hash: 74cf39a0ff80bbe1
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-07
entities: []
source: LEO CDP Wave 3, 2026-06-07
status: seedling
tags:
- java
- virtual-threads
- loom
- executors
- triage
title: Virtual-thread conversion triage - per-run fanout yes, single-thread and shared
  pools no
type: lesson
---

# Virtual-thread conversion triage - per-run fanout yes, single-thread and shared pools no

Triage rule for converting legacy ExecutorService sites to virtual threads (newVirtualThreadPerTaskExecutor), from LEO CDP Wave 3:

✅ CONVERT: per-run, bounded-lifecycle pools doing blocking-I/O fan-out with join/CompletionService semantics (segment export jobs, parallel AQL queries). Thread-per-task is exactly Loom's model; real parallelism stays bounded downstream (DB driver connection pool).

❌ DO NOT convert:
- newSingleThreadExecutor sites - they encode ORDERING (serialized task execution); VT-per-task destroys that silently.
- static shared fixed pools - they are load-shapers; replacing with unbounded VT changes the system's concurrency envelope against the DB/broker and needs load-testing first, not a mechanical swap.
- anything on an event-loop framework's threads (Vert.x 3 has no Loom integration).

Also: on JDK 24+ (JEP 491) synchronized blocks no longer pin carriers, removing the classic VT caveat for legacy code.

## Related

- [[3 Resources/Languages/Java/Records break Gson pre-2.10, ArangoDB driver 6 VPACK, and handlebars getter resolution]]

%% ai-graph-start %%

**Related notes:**
- [[Widening fan-out threads doesn't help once MongoDB is the count bottleneck]]
- [[Records break Gson pre-2.10, ArangoDB driver 6 VPACK, and handlebars getter resolution]]
- [[ManagedExecutorService.execute loses CDI request context]]
- [[Per-pod single-flight kills cache stampede without semantic change]]
- [[CompletableFuture parallel fan-out needs .toList() barrier before joining]]

%% ai-graph-end %%