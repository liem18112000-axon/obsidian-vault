---
title: "Decouple runtime JDK from bytecode target when migrating Java versions"
created: 2026-06-06
type: model
status: seedling
source: "LEO CDP migration planning, session 2026-06-06"
tags: [java, migration, rollback, strategy]
---

# Decouple runtime JDK from bytecode target when migrating Java versions

When migrating a JVM service to a newer Java LTS, treat the **runtime JDK** and the **bytecode target** as two separate dials and never move them together:

1. First run the existing old-target bytecode (e.g. `options.release = 11`) on the new JDK. This step is fully reversible — rollback is just restarting the same jars on the old JDK, per service.
2. Only after the new runtime has soaked in production do you raise the bytecode target. That is the irreversible step: once jars target the new version they no longer load on the old JVM, and rollback means rebuilding.

Corollary for CI: add a `javap -verbose | grep 'major version'` guard so the 'bytecode stays old' invariant is enforced, not assumed. Same logic applies to Gradle upgrades — change the build tool in one phase, the runtime JDK in another, so failures are attributable.

Forward class-file compatibility makes this work: ancient vendored jars (major 51/52/55) load fine on a Java 25 JVM.

## Related

- [[3 Resources/Languages/Java/Gradle/Java 25 requires Gradle 9.1.0 or later, not Gradle 9.0.0]]
- [[Netty 4.1 on JDK 24+ needs --sun-misc-unsafe-memory-access=allow]]
