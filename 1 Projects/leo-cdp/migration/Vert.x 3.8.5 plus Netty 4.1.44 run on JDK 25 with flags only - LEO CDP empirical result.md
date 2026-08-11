---
ai_hash: 1fbe240b6f5ce91b
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-07
entities:
- Vert.x 3.8.5
- Netty 4.1.44
- JDK 25
- LEO CDP
- Corretto 25
- Corretto 11
- ArangoDB
- k6
- Netty 4.1
- JDK 24+
- --sun-misc-unsafe-memory-access=allow
- --enable-native-access=ALL-UNNAMED
- nio/lang/util add-opens
- -Dio.netty.tryReflectionSetAccessible=true
- contingency-ladder step 1
- HTTP
source: LEO CDP migration G2-local, 2026-06-07
status: seedling
tags:
- leo-cdp
- vertx
- netty
- jdk25
- migration
title: Vert.x 3.8.5 plus Netty 4.1.44 run on JDK 25 with flags only - LEO CDP empirical
  result
type: observation
---

# Vert.x 3.8.5 plus Netty 4.1.44 run on JDK 25 with flags only - LEO CDP empirical result

Empirical result (local docker, 2026-06-07): LEO CDP's Vert.x 3.8.5 / Netty 4.1.44 stack **boots and serves HTTP cleanly on Corretto 25** with only the compat flag set (--sun-misc-unsafe-memory-access=allow, --enable-native-access=ALL-UNNAMED, nio/lang/util add-opens, -Dio.netty.tryReflectionSetAccessible=true) - zero InaccessibleObjectException/IllegalAccessError/Unsafe warnings at boot, ArangoDB connection + admin UI serving verified, login page console identical to the Corretto-11 reference. This validates contingency-ladder step 1 (flags only - no Vert.x bump needed so far); load behavior (direct-buffer paths under k6) still pending before declaring G2. The Corretto-25 image is +82MB vs Corretto 11 (752 vs 670MB).

## Related

- [[Netty 4.1 on JDK 24+ needs --sun-misc-unsafe-memory-access=allow]]

%% ai-graph-start %%

**Related notes:**
- [[JDK 25 perf parity must be load-tested - clean boot hid a 15 percent Netty regression]]
- [[Netty 4.1 on JDK 24+ needs --sun-misc-unsafe-memory-access=allow]]
- [[Vert.x 3.9 breaks with hand-pinned Netty 4.1.60+]]
- [[Use JDK_JAVA_OPTIONS for JVM flags in container images]]
- [[Raising bytecode target to 25 on same JVM swept k6 rounds - free-to-positive]]

**Relations:**
- Vert.x 3.8.5 — *runs on* — JDK 25
- Netty 4.1.44 — *runs on* — JDK 25
- LEO CDP — *uses* — Vert.x 3.8.5
- LEO CDP — *uses* — Netty 4.1.44
- Vert.x 3.8.5 — *boots on* — Corretto 25
- Netty 4.1.44 — *boots on* — Corretto 25
- Corretto 25 — *is a distribution of* — JDK 25
- Vert.x 3.8.5 — *serves* — HTTP
- Netty 4.1.44 — *serves* — HTTP
- Vert.x 3.8.5 — *serves* — ArangoDB
- Netty 4.1.44 — *serves* — ArangoDB
- Corretto 25 — *is larger than* — Corretto 11
- k6 — *tests* — load behavior
- Netty 4.1 — *requires flag for JDK 24+* — --sun-misc-unsafe-memory-access=allow
- Vert.x 3.8.5 — *uses flag* — --sun-misc-unsafe-memory-access=allow
- Vert.x 3.8.5 — *uses flag* — --enable-native-access=ALL-UNNAMED
- Vert.x 3.8.5 — *uses flag* — nio/lang/util add-opens
- Vert.x 3.8.5 — *uses flag* — -Dio.netty.tryReflectionSetAccessible=true
- Netty 4.1.44 — *uses flag* — --sun-misc-unsafe-memory-access=allow
- Netty 4.1.44 — *uses flag* — --enable-native-access=ALL-UNNAMED
- Netty 4.1.44 — *uses flag* — nio/lang/util add-opens
- Netty 4.1.44 — *uses flag* — -Dio.netty.tryReflectionSetAccessible=true
- Vert.x 3.8.5 — *is part of* — contingency-ladder step 1
- Netty 4.1.44 — *is part of* — contingency-ladder step 1
- Corretto 11 — *is a reference for* — login page console

%% ai-graph-end %%