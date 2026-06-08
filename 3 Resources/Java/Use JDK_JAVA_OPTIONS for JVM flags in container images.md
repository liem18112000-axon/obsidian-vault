---
title: "Use JDK_JAVA_OPTIONS for JVM flags in container images"
created: 2026-06-06
type: howto
status: seedling
source: "LEO CDP migration Phase 2, 2026-06-06"
tags: [java, docker, jvm-flags, technique]
---

# Use JDK_JAVA_OPTIONS for JVM flags in container images

In container images, put JVM compatibility flags in the `JDK_JAVA_OPTIONS` environment variable instead of the ENTRYPOINT array. The `java` launcher (JDK 9+) reads it automatically, so the flags survive when the ENTRYPOINT/CMD jar is overridden (e.g. one LEO CDP image, five different starter jars chosen at run time) and stay out of the unwieldy exec-form JSON array. The launcher prints 'NOTE: Picked up JDK_JAVA_OPTIONS' to stderr - harmless, and actually useful as proof the flags landed. Distinct from `JAVA_TOOL_OPTIONS` (read by all JVM tools incl. javac, and by the JNI invocation API) - JDK_JAVA_OPTIONS only affects the `java` launcher itself.

## Related

- [[Netty 4.1 on JDK 24+ needs --sun-misc-unsafe-memory-access=allow]]
