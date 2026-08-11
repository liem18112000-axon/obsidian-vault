---
ai_hash: 78080b8db6e1b26f
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-21
entities: []
source: session 2026-07-21 LUZ-156856 gate rework
status: seedling
tags:
- java
- optional
- fault-tolerance
- pattern
- luz-docs
title: Hand-rolled Optional.or fallback chain replaces CDI @Fallback
type: howto
---

# Hand-rolled Optional.or fallback chain replaces CDI @Fallback

When a CDI interceptor-based fallback (`@Fallback`) cannot fire — e.g. self-invocation — an explicit fallback chain does the same job with no container magic and full unit-testability:

```java
private static Optional<Boolean> attempt(BooleanSupplier check, String failureMsg) {
    try {
        return Optional.of(check.getAsBoolean());
    } catch (RuntimeException e) {
        LOGGER.log(Level.WARNING, e, () -> failureMsg);
        return Optional.empty();
    }
}

var complete = attempt(() -> checkL1(...), "L1 failed, fallback to L2")
        .or(() -> attempt(() -> checkL2(...), "L2 failed"))
        .orElse(false);
```

Each layer returns `Optional.empty()` only on **exception** (with a WARNING log), so a legitimate `false` from L1 is a final answer — it does NOT fall through to L2. `Optional.or()` chains any number of layers; `orElse(false)` is the fail-closed default when every layer throws.

Used in luz_docs Materialize/Parallelize gates (L1 = migration-campaign status read, L2 = repository sentinel-field check) after `@Fallback` proved dead on self-invocation — see [[CDI self-invocation bypasses interceptor proxy]].

## Related

- [[CDI self-invocation bypasses interceptor proxy]]

%% ai-graph-start %%

**Related notes:**
- [[CDI self-invocation bypasses interceptor proxy]]
- [[MicroProfile Fallback is dead in plain Mockito unit tests]]
- [[Weld subclass-based interception makes self-invocation intercepted]]
- [[Gate behavior changes must update tests asserting old fallthrough in the same commit]]
- [[Snapshot for rollback must live outside retry boundary]]

%% ai-graph-end %%