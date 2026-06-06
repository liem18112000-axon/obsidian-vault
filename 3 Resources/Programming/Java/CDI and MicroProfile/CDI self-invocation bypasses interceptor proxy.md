---
title: "CDI self-invocation bypasses interceptor proxy"
created: 2026-06-03
type: lesson
status: seedling
source: "luz_docs session 2026-06-03 LUZ-154159"
tags: [java, cdi, microprofile-fault-tolerance, quarkus, spring, gotcha, luz-docs]
---

# CDI self-invocation bypasses interceptor proxy

In CDI (Weld, OpenWebBeans, Quarkus ArC), method-level interceptor bindings such as `@Retry`, `@Fallback`, `@CircuitBreaker`, `@Timeout`, and `@Transactional` only fire when the call goes through the beans **CDI proxy**. A direct `this.foo(...)` call inside the same class is a plain Java invocation — it never touches the proxy, so none of the interceptors run.

**Symptom — silent failure.** The code compiles, looks correct, and works on the happy path. Under transient failure, the retry / fallback / circuit-breaker simply does not activate. Bugs only surface in production-shape error scenarios.

**Fix — inject self.** Inject the bean into itself and call the decorated method through the injected reference:

```java
@ApplicationScoped
class MyService {
    @Inject
    private MyService self; // CDI hands back the proxy

    void publicEntry(...) {
        self.retriedMethod(...); // goes through proxy → @Retry applies
    }

    @Retry(retryOn = SomeException.class)
    @Fallback(fallbackMethod = "fallback")
    Result retriedMethod(...) { ... }

    Result fallback(...) { ... }
}
```

**When this matters.** Any time the decorated method needs to be called from a sibling method of the same `@ApplicationScoped` / `@RequestScoped` / `@Dependent` bean. Cross-bean calls (Facade → Service) already go through the proxy and do not need self-injection.

**Discovered while** wiring snapshot + retry + rollback into `MaterializeFolderParentChangeService.onFolderParentChange` → `cascadeWithRetry` on the luz_docs LUZ-154159 branch. The first attempt placed `@Retry` + `@Fallback` on `cascadeWithRetry` and called it via `this`; the fallback never fired in failure tests until the `@Inject self` was added.

**Same trap exists in Spring.** `@Transactional`, `@Cacheable`, `@Async`, `@Retryable` (Spring Retry) use AOP proxies — same self-invocation gotcha, same workaround (inject self via `@Lazy` self-reference or call through `AopContext.currentProxy()`).

## Related

- [[MicroProfile Fault Tolerance]]
- [[luz-docs materialize cascade]]
