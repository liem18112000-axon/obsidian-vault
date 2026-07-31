---
ai_hash: fff911668f777f42
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-06
entities: []
source: LEO CDP migration Phase 2, 2026-06-06
status: seedling
tags:
- java
- docker
- jvm-flags
- technique
title: Use JDK_JAVA_OPTIONS for JVM flags in container images
type: howto
---

# Use JDK_JAVA_OPTIONS for JVM flags in container images

In container images, put JVM compatibility flags in the `JDK_JAVA_OPTIONS` environment variable instead of the ENTRYPOINT array. The `java` launcher (JDK 9+) reads it automatically, so the flags survive when the ENTRYPOINT/CMD jar is overridden (e.g. one LEO CDP image, five different starter jars chosen at run time) and stay out of the unwieldy exec-form JSON array. The launcher prints 'NOTE: Picked up JDK_JAVA_OPTIONS' to stderr - harmless, and actually useful as proof the flags landed. Distinct from `JAVA_TOOL_OPTIONS` (read by all JVM tools incl. javac, and by the JNI invocation API) - JDK_JAVA_OPTIONS only affects the `java` launcher itself.

## Related

- [[Netty 4.1 on JDK 24+ needs --sun-misc-unsafe-memory-access=allow]]

%% ai-graph-start %%

**Related notes:**
- [[Netty 4.1 on JDK 24+ needs --sun-misc-unsafe-memory-access=allow]]
- [[Decouple runtime JDK from bytecode target when migrating Java versions]]
- [[Vert.x 3.8.5 plus Netty 4.1.44 run on JDK 25 with flags only - LEO CDP empirical result]]
- [[JDK 25 with CompactObjectHeaders beat JDK 11 by 7pct rps in LEO CDP k6 AB]]
- [[GitHub Actions runners pick JDK from inherited JAVA_HOME, not PATH]]

%% ai-graph-end %%