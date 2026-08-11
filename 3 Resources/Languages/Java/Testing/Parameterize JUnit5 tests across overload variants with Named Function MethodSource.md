---
ai_hash: 1e7dfe7cfe919733
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-02
entities: []
tags:
- junit5
- parameterized-tests
- java
- test-design
---

# Parameterize JUnit5 tests across overload variants with `Named<Function>` `@MethodSource`

When a class exposes two overloads that share most structural behavior (e.g. `addFolderObjects(JsonArray)` and `addFolderObjects(JsonArray, String)`), naive coverage produces two mirror-test bodies per behavior — heavy duplication that drifts as the code evolves.

Fold them with a `@MethodSource` that yields each overload as a `Named<Function<Input, Output>>`. Each parameterized test takes the invoker and calls `invoke.apply(...)`; JUnit's display name shows which variant ran.

```java
static Stream<Named<Function<JsonArray, JsonArray>>> variants() {
    return Stream.of(
        Named.of("addFolderObjects(docs, null)", docs -> Cls.addFolderObjects(docs, null)),
        Named.of("addFolderObjects(docs)", Cls::addFolderObjects));
}

@ParameterizedTest
@MethodSource("variants")
void empty_input_returns_empty_array(Function<JsonArray, JsonArray> invoke) {
    assertTrue(invoke.apply(Json.createArrayBuilder().build()).isEmpty());
}
```

## When to apply

- Overloads share ≥ ~4 structural behaviors (empty input, missing field, multiple items, passthrough, …).
- Variant-specific behavior still gets its own dedicated `@Test` — don't force-fit it into the parameterized suite.

## Why `Named<Function<>>` not raw `Function<>`

Without `Named.of(...)`, JUnit prints lambda class names (`Cls$$Lambda$42/0x...`) in the test report — unreadable. `Named` wraps each invoker with a human-readable label.

## Trade-off

- Loses per-variant test names ("noArg_empty_input_…") in favor of one name + variant suffix.
- Stack traces point at the parameterized body, not a unique `@Test` method — slightly harder to bisect failures by method name alone, but the `Named` label disambiguates.

Related: [[Mock at the facade boundary after consolidating logic behind a facade method]]

%% ai-graph-start %%

**Related notes:**
- [[Mock at the facade boundary after consolidating logic behind a facade method]]
- [[A delegating overload changes less code than widening an existing method signature]]
- [[Shape-keyed test mocks break when production query shapes change]]
- [[Mockito strict stubs flag mismatched-arg calls on a stubbed method as failures]]
- [[JUnit5 @BeforeAll must be static - non-static masks every test in the class]]

%% ai-graph-end %%