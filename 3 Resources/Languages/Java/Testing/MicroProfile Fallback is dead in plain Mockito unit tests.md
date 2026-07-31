---
ai_hash: 131af70e280d2cb7
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-21
entities: []
source: session 2026-07-21 LUZ-156856
status: seedling
tags:
- fault-tolerance
- mockito
- testing
- gotcha
- luz-docs
title: MicroProfile Fallback is dead in plain Mockito unit tests
type: lesson
---

# MicroProfile Fallback is dead in plain Mockito unit tests

MicroProfile Fault Tolerance annotations (`@Fallback`, `@Retry`, ...) are CDI-interceptor driven. A plain Mockito unit test (`@ExtendWith(MockitoExtension.class)` + `@InjectMocks`) instantiates the bare class — no container, no intercepted subclass — so the annotations are inert: an exception in the annotated method propagates instead of triggering the fallback.

Consequences:
- Fallback/retry paths cannot be unit-tested this way; they need a container test (Arquillian) or direct invocation of the fallback method.
- When refactoring a hand-rolled try/catch fallback into `@Fallback`, existing unit tests that asserted the fallback behavior will start failing and must be removed or rewritten (hit in `MaterializeGateTest`, LUZ-156856).

Why it differs from production: on WildFly the interceptor DOES fire, even on self-invocation — see [[Weld subclass-based interception makes self-invocation intercepted]].

## Related

- [[Weld subclass-based interception makes self-invocation intercepted]]

Follow-on gotcha: adding a test that skips the shared `@BeforeEach` stubs trips strict-stubs (`UnnecessaryStubbingException`) on the whole class — mark the shared stub `lenient().when(...)` instead of duplicating stubs per test.

Final gate shape (LUZ-156856): hybrid belt-and-braces — `@Fallback(L2)` on the L1 check handles service degradation in-container, PLUS an outer catch-all in the gate method that returns false and caches INCOMPLETE when even L2 throws (fail-closed + negative caching preserved). Outer catch also makes the unit-testable no-interceptor path deterministic (throw → false).

%% ai-graph-start %%

**Related notes:**
- [[CDI self-invocation bypasses interceptor proxy]]
- [[Weld subclass-based interception makes self-invocation intercepted]]
- [[Hand-rolled Optional.or fallback chain replaces CDI @Fallback]]
- [[Mockito strict stubs flag mismatched-arg calls on a stubbed method as failures]]
- [[Gate behavior changes must update tests asserting old fallthrough in the same commit]]

%% ai-graph-end %%