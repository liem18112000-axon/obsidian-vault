---
ai_hash: ecf497bab813a242
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-06
entities: []
source: leo-cdp-framework build.gradle fix 2026-06-06
status: seedling
tags:
- gradle
- groovy
- closure-delegate
- gotcha
title: Fully-qualified class names fail inside Gradle closures with a non-Project
  delegate
type: lesson
---

# Fully-qualified class names fail inside Gradle closures with a non-Project delegate

Writing a **fully-qualified class name** inside a Gradle/Groovy configuration closure whose delegate is not the Project can fail at evaluation with e.g.:

```
Could not get unknown property "org" for { ... } of type org.gradle.api.internal.attributes.DefaultMutableAttributeContainer
```

**Why:** Gradle Groovy scripts resolve a bare leading identifier (the `org` in `org.gradle.api.attributes.java.TargetJvmEnvironment...`) as a **dynamic property access**, *before* interpreting the dotted path as a package/class reference. The error names whatever object is the current resolution target:
- inside a closure like `attributes { ... }` → `Could not get unknown property 'org' for ... DefaultMutableAttributeContainer` (delegate = the AttributeContainer);
- in the script body → `Could not get unknown property 'org' for root project '...' of type org.gradle.api.Project`.

**So "resolve it in a `def` outside the closure" does NOT fix it** — the Project also has no `org` property. (I tried this; it failed the same way.)

**The fix: `import` the class at the top and use its simple, capitalized name** — a capitalized identifier is resolved as a class, not a dynamic property:
```gradle
import org.gradle.api.attributes.Attribute   // at the very top of build.gradle
...
configurations.all {
   attributes { attribute(Attribute.of("org.gradle.jvm.environment", String), "standard-jvm") }
}
```

**Caveat that bit me next:** an `import` only works if the class actually exists in *that* Gradle version. Importing `org.gradle.api.attributes.java.TargetJvmEnvironment` failed with `unable to resolve class` because that type **does not exist in Gradle 6.9.4** (only `TargetJvmVersion` does). The string-name attribute form above sidesteps the class entirely. See [[3 Resources/Languages/Java/Gradle/Guava jreandroid variant ambiguity declare TargetJvmEnvironment standard-jvm]].

Lesson learned the hard way: verify build-script edits in the real toolchain (here, the Docker `build` stage) before pushing — three "looks-fine" attempts each broke build-script evaluation in a different way.

## Related
- [[3 Resources/Languages/Java/Gradle/Guava jreandroid variant ambiguity declare TargetJvmEnvironment standard-jvm]]
- [[LEO CDP CI provisions deps CI-natively, pinned to devops-script versions for parity]]

%% ai-graph-start %%

**Related notes:**
- [[Guava jreandroid variant ambiguity declare TargetJvmEnvironment standard-jvm]]
- [[String-typed org.gradle.jvm.environment attribute collides with Gradle 7+ typed TargetJvmEnvironment]]
- [[Gradle 9 forbids attributes() on declarable configurations in configurations.all]]
- [[Check git check-ignore -v when adding a Gradle wrapper to a legacy repo]]
- [[gradlew wrapper upgrades run under the OLD Gradle version - pick the JDK accordingly]]

%% ai-graph-end %%