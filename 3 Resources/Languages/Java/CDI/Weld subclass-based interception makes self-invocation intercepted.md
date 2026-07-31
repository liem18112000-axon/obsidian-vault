---
title: "Weld subclass-based interception makes self-invocation intercepted"
created: 2026-07-21
type: concept
status: seedling
source: "session 2026-07-21 LUZ-156856"
tags: [cdi, weld, wildfly, fault-tolerance, gotcha, luz-docs]
---

# Weld subclass-based interception makes self-invocation intercepted

In Weld (WildFly's CDI implementation), interceptors are applied by generating an *intercepted subclass* of the bean — the contextual instance IS the subclass. Because dispatch is virtual, calling an annotated method via `this` from another method of the same bean still goes through the interceptor chain.

This is the opposite of Spring AOP (and a common source of confusion): Spring proxies wrap the bean, so self-invocation bypasses advice. In Weld, self-invocation is intercepted.

Practical consequence: MicroProfile Fault Tolerance annotations (`@Fallback`, `@Retry`, `@CircuitBreaker`) on a package-private method work even when the caller is another method in the same bean. Used in `MaterializeGate` (LUZ-156856): `isMaterializationComplete` calls `isCompletedCheckL1` directly, and `@Fallback(fallbackMethod = "isCompletedCheckL2")` still fires on WildFly 26.

Caveat: only true in a running container — see [[MicroProfile Fallback is dead in plain Mockito unit tests]].

## Related

- [[MicroProfile Fallback is dead in plain Mockito unit tests]]

Portability nuance: the Interceptors spec only guarantees interception for invocations through a bean reference (client proxy) — self-invocation interception is Weld implementation behavior, not a spec promise. Portable alternative: `@Inject MaterializeGate self` (client-proxy self-injection, legal for normal-scoped beans) and call `self.method()`; costs an extra field and NPEs under `@InjectMocks` unless mocked. On a pinned WildFly runtime, direct call is fine.
