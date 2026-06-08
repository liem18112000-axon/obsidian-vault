---
title: "Java 25 requires Gradle 9.1.0 or later, not Gradle 9.0.0"
created: 2026-06-06
type: lesson
status: seedling
source: "LEO CDP migration planning, session 2026-06-06"
tags: [gradle, java25, compatibility, leo-cdp]
---

# Java 25 requires Gradle 9.1.0 or later, not Gradle 9.0.0

Gradle 9.0.0 supports running and compiling at most Java 24. Support for Java 25 (both the daemon and toolchains) only landed in Gradle 9.1.0. So a requirement like "migrate to Gradle 9 + Java 25" effectively means **Gradle >= 9.1** — picking 9.0.0 dead-ends the Java half of the migration.

Also relevant: Gradle 9 itself requires JDK 17+ to run, but you can still emit older bytecode via `options.release` (e.g. run the daemon on JDK 25 while producing Java 11 classes).

Source: Gradle compatibility matrix + Gradle 9.1.0 release notes (verified 2026-06).

## Related

- [[Decouple runtime JDK from bytecode target when migrating Java versions]]
