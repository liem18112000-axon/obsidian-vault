---
ai_hash: 1809d36d1861d1d5
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-06
entities: []
source: LEO CDP migration planning, session 2026-06-06
status: seedling
tags:
- netty
- java25
- jep498
- vertx
- jvm-flags
title: Netty 4.1 on JDK 24+ needs --sun-misc-unsafe-memory-access=allow
type: lesson
---

# Netty 4.1 on JDK 24+ needs --sun-misc-unsafe-memory-access=allow

Netty 4.1.x uses `sun.misc.Unsafe` memory-access methods, which JDK 24+ flags under JEP 498. To run old Netty (and anything embedding it, like Vert.x 3) on JDK 24/25 without warnings or future breakage, pass:

```
--sun-misc-unsafe-memory-access=allow
--add-opens=java.base/java.nio=ALL-UNNAMED
--add-opens=java.base/sun.nio.ch=ALL-UNNAMED
-Dio.netty.tryReflectionSetAccessible=true
```

The last flag lets old Netty actually use the (now-opened) reflective direct-buffer path instead of a slower fallback. JNI bits (netty-native, snappy/zstd) additionally want `--enable-native-access=ALL-UNNAMED` (JEP 472).

The real fix is Netty **4.2.3+**, which is Unsafe-free via the MemorySegment API. Netty 4.1.120/121 briefly disabled Unsafe by default on JDK 24+, reverted in 4.1.122.

Source: netty.io wiki 'Java 24 and sun.misc.Unsafe' (verified 2026-06).

## Related

- [[Vert.x 3.9 breaks with hand-pinned Netty 4.1.60+]]
- [[3 Resources/Languages/Java/Gradle/Java 25 requires Gradle 9.1.0 or later, not Gradle 9.0.0]]

%% ai-graph-start %%

**Related notes:**
- [[Vert.x 3.9 breaks with hand-pinned Netty 4.1.60+]]
- [[Vert.x 3.8.5 plus Netty 4.1.44 run on JDK 25 with flags only - LEO CDP empirical result]]
- [[Decouple runtime JDK from bytecode target when migrating Java versions]]
- [[Use JDK_JAVA_OPTIONS for JVM flags in container images]]
- [[JDK 25 perf parity must be load-tested - clean boot hid a 15 percent Netty regression]]

%% ai-graph-end %%