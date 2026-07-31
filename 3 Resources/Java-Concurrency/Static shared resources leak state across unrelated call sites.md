---
title: "Static shared resources leak state across unrelated call sites"
created: 2026-07-10
type: lesson
status: seedling
source: "luz_docs MaterializeMigrationExecutor.java review, 2026-07-10"
tags: [java, concurrency, static, resource-leak, gotcha]
---

# Static shared resources leak state across unrelated call sites

A `static` field (semaphore, cache, counter, connection pool) is shared across every call site and every request in the JVM, so a resource leak in one code path — even one that runs rarely, like an error branch — silently consumes shared capacity that unrelated call sites depend on. There is no isolation between "my one migration job leaked a permit" and "every other job that needs that same static semaphore."

This is what makes small acquire/release bugs dangerous: the failure does not stay local. Three leaked permits on a `static Semaphore(3)` do not just break the one loop that leaked them — they exhaust the semaphore for the whole application, hanging every future caller that acquires it, until a restart resets the static state.

**Rule of thumb**: when reviewing code that touches a `static` mutable resource, check every exit path (normal, exception, early return) accounts for release/cleanup — a single missed path can degrade the entire process, not just the current request.

## Related
[[Semaphore permit leak when risky code sits between acquire and try]]
