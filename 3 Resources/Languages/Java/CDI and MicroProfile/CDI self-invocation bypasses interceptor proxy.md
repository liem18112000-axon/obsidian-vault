---
ai_hash: c48b7159ee1f3793
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-03
entities: []
status: seedling
tags:
- java
- cdi
- microprofile
- microprofile-fault-tolerance
- fault-tolerance
- quarkus
- spring
- code-review
- gotcha
- luz-docs
title: CDI self-invocation bypasses interceptor proxy
type: gotcha
---

# CDI self-invocation bypasses interceptor proxy

Method-level interceptor bindings — MicroProfile Fault Tolerance `@Retry`, `@Fallback`, `@Timeout`, `@CircuitBreaker`, plus `@Transactional`, `@CacheResult`, and any custom interceptor — only fire when the call goes through the bean's **CDI client proxy**. A bare `this.foo(...)` (or implicit `foo(...)`) call from a sibling method of the same bean is a plain Java invocation: the interceptor never runs.

**Symptom — silent inertness.** Annotation present, code compiles, happy path fine, tests pass. Under failure the retry/fallback/circuit-breaker simply never activates; nothing in the logs. Plain Mockito unit tests can't catch it either (no container = no interceptor either way).

**Fix — inject self and call through the proxy:**

```java
@ApplicationScoped
class MyService {
    @Inject private MyService self;              // CDI hands back the proxy

    void publicEntry(...) {
        self.retriedMethod(...);                 // → @Retry/@Fallback apply
    }

    @Retry(retryOn = SomeException.class)
    @Fallback(fallbackMethod = "fallback")
    Result retriedMethod(...) { ... }
}
```

Alternatives: move the annotated method to another bean, or hand-roll the logic — see [[Hand-rolled Optional.or fallback chain replaces CDI @Fallback]]. Cross-bean calls (Facade → Service) already go through the proxy and need nothing.

**Review smell:** an `@Retry`/`@Fallback`/`@Async`/`@Transactional` method whose only callers are in the same class. Grep for intra-class calls to annotated methods.

**Caveat — Weld is the exception.** WildFly's Weld uses *subclass-based* interception, so self-invocation IS intercepted there; the proxy rule is the portable/spec guarantee. See [[Weld subclass-based interception makes self-invocation intercepted]].

**Same trap in Spring AOP** — `@Transactional`, `@Cacheable`, `@Async`, `@Retryable` (Spring Retry); same workaround (`@Lazy` self-reference or `AopContext.currentProxy()`).

Hit repeatedly in luz_docs: LUZ-154159 `MaterializeFolderParentChangeService.onFolderParentChange` (fallback dead until `@Inject self`; also `getSnapshot(...)` still called via `this` on the next line, killing its `@Retry` *and* its `@Fallback` 500-mapping), LUZ-154804 `MaterializeCascadeService.rematerializeDocument` (annotations deleted as inert), LUZ-156856 `MaterializeGate`/`ParallelizeGate` `@Fallback(fallbackMethod = "isCompletedCheckL2")` on `isCompletedCheckL1`.

## Related

- [[Weld subclass-based interception makes self-invocation intercepted]]
- [[Hand-rolled Optional.or fallback chain replaces CDI @Fallback]]
- [[MicroProfile Fallback is dead in plain Mockito unit tests]]
- [[MicroProfile Fault Tolerance]]
- [[luz-docs materialize cascade]]
- [[luz_docs materialize passive retry via cascade markers]]

%% ai-graph-start %%

**Related notes:**
- [[Weld subclass-based interception makes self-invocation intercepted]]
- [[MicroProfile Fallback is dead in plain Mockito unit tests]]
- [[Hand-rolled Optional.or fallback chain replaces CDI @Fallback]]
- [[Snapshot for rollback must live outside retry boundary]]
- [[CDI decorators and interceptors never fire on MicroProfile REST client proxies]]

%% ai-graph-end %%