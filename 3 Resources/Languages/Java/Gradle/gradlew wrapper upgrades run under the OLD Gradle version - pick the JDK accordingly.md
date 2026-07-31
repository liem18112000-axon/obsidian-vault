---
ai_hash: 2494d058627bf561
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-06
entities: []
source: LEO CDP migration Phase 1c, 2026-06-06
status: seedling
tags:
- gradle
- wrapper
- gotcha
- jdk25
title: gradlew wrapper upgrades run under the OLD Gradle version - pick the JDK accordingly
type: lesson
---

# gradlew wrapper upgrades run under the OLD Gradle version - pick the JDK accordingly

`./gradlew wrapper --gradle-version X` is executed by the **currently installed** wrapper version, not by X. So the JDK running the command must satisfy the OLD Gradle's compatibility ceiling. Stepping LEO CDP 8.14.3 → 9.1.0 on a JDK 25 JAVA_HOME failed with *'Unsupported class file major version 69'* (Gradle 8.14 caps at Java 24); the same command on JDK 21 succeeded, after which Gradle 9.1 itself runs fine on JDK 25. Rule: bump the wrapper with a JDK both versions support, then switch the daemon JDK.

## Related

- [[3 Resources/Languages/Java/Gradle/Java 25 requires Gradle 9.1.0 or later, not Gradle 9.0.0]]

%% ai-graph-start %%

**Related notes:**
- [[Java 25 requires Gradle 9.1.0 or later, not Gradle 9.0.0]]
- [[Decouple runtime JDK from bytecode target when migrating Java versions]]
- [[Check git check-ignore -v when adding a Gradle wrapper to a legacy repo]]
- [[String-typed org.gradle.jvm.environment attribute collides with Gradle 7+ typed TargetJvmEnvironment]]
- [[GitHub Actions runners pick JDK from inherited JAVA_HOME, not PATH]]

%% ai-graph-end %%