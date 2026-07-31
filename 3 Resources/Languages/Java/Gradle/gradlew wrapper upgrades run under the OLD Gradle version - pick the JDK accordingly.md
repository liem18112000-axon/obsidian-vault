---
title: "gradlew wrapper upgrades run under the OLD Gradle version - pick the JDK accordingly"
created: 2026-06-06
type: lesson
status: seedling
source: "LEO CDP migration Phase 1c, 2026-06-06"
tags: [gradle, wrapper, gotcha, jdk25]
---

# gradlew wrapper upgrades run under the OLD Gradle version - pick the JDK accordingly

`./gradlew wrapper --gradle-version X` is executed by the **currently installed** wrapper version, not by X. So the JDK running the command must satisfy the OLD Gradle's compatibility ceiling. Stepping LEO CDP 8.14.3 → 9.1.0 on a JDK 25 JAVA_HOME failed with *'Unsupported class file major version 69'* (Gradle 8.14 caps at Java 24); the same command on JDK 21 succeeded, after which Gradle 9.1 itself runs fine on JDK 25. Rule: bump the wrapper with a JDK both versions support, then switch the daemon JDK.

## Related

- [[Java 25 requires Gradle 9.1.0 or later]]
- [[not Gradle 9.0.0]]
