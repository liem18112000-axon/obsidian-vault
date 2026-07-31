---
ai_hash: 74ac2deceacadf56
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-06
entities: []
source: leo-cdp-framework compileTestJava fix 2026-06-06
status: seedling
tags:
- gradle
- guava
- dependency-resolution
- java
- gotcha
title: 'Guava jre/android variant ambiguity: declare TargetJvmEnvironment standard-jvm'
type: lesson
---

# Guava jre/android variant ambiguity: declare TargetJvmEnvironment standard-jvm

Gradle failing with `Could not resolve com.google.guava:guava` and `cannot choose between ... androidApiElements / jreApiElements` is a **variant-selection ambiguity**, not a missing artifact. Guava publishes two variants of the same coordinate via Gradle Module Metadata — `-jre` (standard JVM) and `-android` — distinguished by the attribute `org.gradle.jvm.environment`. When several transitive deps request different Guava versions and the consuming configuration does not declare which JVM environment it targets, Gradle cannot disambiguate and fails.

It can fail on `testCompileClasspath` while `compileClasspath` (main) succeeds — e.g. adding `selenium-java` to test deps pulls a newer `guava:*-jre` whose metadata makes the choice ambiguous on the test classpath only.

**Fix — set the `org.gradle.jvm.environment` attribute to `standard-jvm` so Gradle picks the `-jre` variant.** Use the **string name/value** form, which is version-robust:

```gradle
import org.gradle.api.attributes.Attribute   // top of build.gradle

configurations.all {
   attributes {
      attribute(Attribute.of("org.gradle.jvm.environment", String), "standard-jvm")
   }
}
```

**Why the string form, not the typed class:** the typed approach
`attribute(TargetJvmEnvironment.TARGET_JVM_ENVIRONMENT_ATTRIBUTE, objects.named(TargetJvmEnvironment, TargetJvmEnvironment.STANDARD_JVM))`
only works on a Gradle new enough to *have* `org.gradle.api.attributes.java.TargetJvmEnvironment`. **Gradle 6.9.4 does NOT ship that class** (only `TargetJvmVersion`), so importing it fails with `unable to resolve class`. The string-keyed `Attribute.of(...)` needs no such class and Gradle's attribute desugaring still matches it against the producer's typed variant. Verified on Gradle 6.9.4: `compileTestJava` → BUILD SUCCESSFUL.

Apply on `configurations.all` so both main and test classpaths resolve consistently. `-jre` is the correct choice for a server-side build. Don't write the class name as a fully-qualified `org.gradle...` reference in the script — see [[Fully-qualified class names fail inside Gradle closures with a non-Project delegate]].

## Related
- [[Fully-qualified class names fail inside Gradle closures with a non-Project delegate]]
- [[LEO CDP CI provisions deps CI-natively, pinned to devops-script versions for parity]]

%% ai-graph-start %%

**Related notes:**
- [[String-typed org.gradle.jvm.environment attribute collides with Gradle 7+ typed TargetJvmEnvironment]]
- [[Fully-qualified class names fail inside Gradle closures with a non-Project delegate]]
- [[Gradle 9 forbids attributes() on declarable configurations in configurations.all]]
- [[junit-platform-launcher needs an explicit version without a junit-bom]]
- [[Gradle 9 failOnNoDiscoveredTests exposes never-configured JUnit platform]]

%% ai-graph-end %%