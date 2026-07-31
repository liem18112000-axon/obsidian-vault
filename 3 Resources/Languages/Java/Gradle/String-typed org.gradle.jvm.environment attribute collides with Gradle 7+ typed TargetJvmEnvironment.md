---
title: "String-typed org.gradle.jvm.environment attribute collides with Gradle 7+ typed TargetJvmEnvironment"
created: 2026-06-06
type: lesson
status: seedling
source: "LEO CDP migration Phase 1a, 2026-06-06"
tags: [gradle, gotcha, guava, attributes, leo-cdp]
---

# String-typed org.gradle.jvm.environment attribute collides with Gradle 7+ typed TargetJvmEnvironment

A Gradle-6 era workaround that declares a **String-typed** attribute named `org.gradle.jvm.environment` (to force Guava's -jre variant) hard-fails configuration on Gradle 7+ with: *"Cannot have two attributes with the same name but different types. This container already has an attribute named 'org.gradle.jvm.environment' of type ...TargetJvmEnvironment"*.

Why: Gradle 7 introduced the typed `TargetJvmEnvironment` attribute and applies it built-in; a same-named String attribute now collides instead of being merged.

Fix (works 7.x through 9.x):
```groovy
configurations.all {
  attributes {
    attribute(TargetJvmEnvironment.TARGET_JVM_ENVIRONMENT_ATTRIBUTE,
              objects.named(TargetJvmEnvironment, TargetJvmEnvironment.STANDARD_JVM))
  }
}
```
Found while stepping LEO CDP 6.9.4 → 7.6.4: this was the FIRST failure of the upgrade, before any of the documented removals (maven plugin etc.) bit. Lesson: grep legacy build scripts for `Attribute.of("org.gradle.` string attributes before any major Gradle bump.

## Related

- [[Java 25 requires Gradle 9.1.0 or later, not Gradle 9.0.0]]
- [[Gradle copy{} inside a task body runs at configuration time, not execution]]
- [[Guava jreandroid variant ambiguity declare TargetJvmEnvironment standard-jvm]]
