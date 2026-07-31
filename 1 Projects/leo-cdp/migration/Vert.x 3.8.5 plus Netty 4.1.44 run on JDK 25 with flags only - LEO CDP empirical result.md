---
title: "Vert.x 3.8.5 plus Netty 4.1.44 run on JDK 25 with flags only - LEO CDP empirical result"
created: 2026-06-07
type: observation
status: seedling
source: "LEO CDP migration G2-local, 2026-06-07"
tags: [leo-cdp, vertx, netty, jdk25, migration]
---

# Vert.x 3.8.5 plus Netty 4.1.44 run on JDK 25 with flags only - LEO CDP empirical result

Empirical result (local docker, 2026-06-07): LEO CDP's Vert.x 3.8.5 / Netty 4.1.44 stack **boots and serves HTTP cleanly on Corretto 25** with only the compat flag set (--sun-misc-unsafe-memory-access=allow, --enable-native-access=ALL-UNNAMED, nio/lang/util add-opens, -Dio.netty.tryReflectionSetAccessible=true) - zero InaccessibleObjectException/IllegalAccessError/Unsafe warnings at boot, ArangoDB connection + admin UI serving verified, login page console identical to the Corretto-11 reference. This validates contingency-ladder step 1 (flags only - no Vert.x bump needed so far); load behavior (direct-buffer paths under k6) still pending before declaring G2. The Corretto-25 image is +82MB vs Corretto 11 (752 vs 670MB).

## Related

- [[Netty 4.1 on JDK 24+ needs --sun-misc-unsafe-memory-access=allow]]
