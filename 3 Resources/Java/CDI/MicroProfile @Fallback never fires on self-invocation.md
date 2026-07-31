---
title: "MicroProfile @Fallback never fires on self-invocation"
created: 2026-07-21
type: gotcha
status: seedling
source: "session 2026-07-21 LUZ-156856 gate rework"
tags: [java, cdi, microprofile, fault-tolerance, gotcha, luz-docs]
---

# MicroProfile @Fallback never fires on self-invocation

CDI interceptors (including MicroProfile Fault Tolerance `@Fallback`) only wrap invocations that go **through the bean proxy**. A method calling another method of the same bean (`this.isCompletedCheckL1(...)`) bypasses the proxy, so the `@Fallback` interceptor never fires — the fallback is silently dead in production, and it is also invisible to plain Mockito unit tests (no container = no interceptor either way).

Symptom shape: annotation present, code compiles, tests pass, yet an exception in the annotated method propagates instead of triggering the fallback.

Applies to all CDI interceptor-based annotations on self-invoked methods: `@Fallback`, `@Retry`, `@Timeout`, `@Transactional`, `@CacheResult`, custom interceptors.

Fixes: call through the injected proxy (self-injection), move the annotated method to another bean, or drop the annotation and hand-roll the fallback — see [[Hand-rolled Optional.or fallback chain replaces CDI @Fallback]].

Hit in luz_docs LUZ-156856: MaterializeGate/ParallelizeGate had `@Fallback(fallbackMethod = "isCompletedCheckL2")` on `isCompletedCheckL1`, called from `isMaterializationComplete` on the same bean — never fired.

## Related

- [[Hand-rolled Optional.or fallback chain replaces CDI @Fallback]]
