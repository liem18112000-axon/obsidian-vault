---
title: "Netty 4.1 on JDK 24+ needs --sun-misc-unsafe-memory-access=allow"
created: 2026-06-06
type: lesson
status: seedling
source: "LEO CDP migration planning, session 2026-06-06"
tags: [netty, java25, jep498, vertx, jvm-flags]
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
