---
title: "Virtual-thread conversion triage - per-run fanout yes, single-thread and shared pools no"
created: 2026-06-07
type: lesson
status: seedling
source: "LEO CDP Wave 3, 2026-06-07"
tags: [java, virtual-threads, loom, executors, triage]
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

- [[Records break Gson pre-2.10]]
- [[ArangoDB driver 6 VPACK]]
- [[and handlebars getter resolution]]
