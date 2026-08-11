---
ai_hash: 8859448ecbfe2a15
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-06
entities: []
source: LEO CDP migration Phase 1c, 2026-06-06
status: seedling
tags:
- gradle
- gradle9
- gotcha
- configurations
- leo-cdp
title: Gradle 9 forbids attributes() on declarable configurations in configurations.all
type: lesson
---

# Gradle 9 forbids attributes() on declarable configurations in configurations.all

Gradle 9 enforces configuration roles strictly: calling `attributes { ... }` inside `configurations.all` fails with *'Method call not allowed ... permitted usage(s): Declarable'* because dependency-scope configurations (`implementation`, `compileOnly`, `runtimeOnly`) are Declarable-only — attributes are reserved for Resolvable/Consumable configurations (`compileClasspath`, `runtimeClasspath`, `apiElements`...).

Fix the blanket-attribute idiom by gating:
```groovy
configurations.configureEach {
   exclude module: 'x'                       // excludes are still legal everywhere
   if (it.canBeResolved || it.canBeConsumed) {
      attributes { attribute(...) }
   }
}
```
Worked on Gradle 6-8; broke only at 9. Third empirical Gradle-9 break in the LEO CDP migration after the typed TargetJvmEnvironment collision (7.x) and space-assignment deprecations (8.x).

## Related

- [[String-typed org.gradle.jvm.environment attribute collides with Gradle 7+ typed TargetJvmEnvironment]]

Refinement: gating on `canBeResolved || canBeConsumed` is still not enough — the legacy **consumable** `:archives` configuration also rejects `attributes()` (deprecation warning in Gradle 9, error in Gradle 10). The correct gate for a variant-selection attribute is **`it.canBeResolved` only**: variant selection happens at resolution time, so consumable configurations never need the pin.

%% ai-graph-start %%

**Related notes:**
- [[String-typed org.gradle.jvm.environment attribute collides with Gradle 7+ typed TargetJvmEnvironment]]
- [[Fully-qualified class names fail inside Gradle closures with a non-Project delegate]]
- [[Guava jreandroid variant ambiguity declare TargetJvmEnvironment standard-jvm]]
- [[Gradle 9 failOnNoDiscoveredTests exposes never-configured JUnit platform]]
- [[Baseline-diff gates must compare post-build to post-build when artifacts are committed]]

%% ai-graph-end %%