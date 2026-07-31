---
ai_hash: 802a393b30b11d93
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-06
entities: []
source: LEO CDP migration planning, session 2026-06-06
status: seedling
tags:
- java
- jar
- audit
- vendored-deps
- leo-cdp
title: Binary-only vendored jars pin the API level of everything they link
type: howto
---

# Binary-only vendored jars pin the API level of everything they link

A vendored binary-only jar (no source available) silently pins the API level of every library it links against. In LEO CDP, `ext-lib/rfx-core-1.0.jar` links `io.vertx.core`, `io.netty.handler`, Jedis, and Kafka APIs — so any Vert.x 4/5 upgrade is blocked until its source is recovered, even though the application's own code compiles fine.

Technique to discover what a binary-only jar links to (no decompiler needed) — grep the constant pools of its class files for package-path strings:

```bash
unzip -p the.jar $(unzip -Z1 the.jar | grep '\.class$') | grep -aoE '(io/vertx|io/netty|sun/misc|jdk/internal|javax/xml/bind)[a-zA-Z/]*' | sort -u
```

The same trick audits jars for removed/internal JDK APIs (sun.misc, jdk.internal, JAXB, Nashorn) before a Java major-version migration. Bytecode major version of the first class (`od` bytes 7-8) tells you its minimum JVM.

## Related

- [[Decouple runtime JDK from bytecode target when migrating Java versions]]

%% ai-graph-start %%

**Related notes:**
- [[Decouple runtime JDK from bytecode target when migrating Java versions]]
- [[Vert.x 3.9 breaks with hand-pinned Netty 4.1.60+]]
- [[Vert.x 3.8.5 plus Netty 4.1.44 run on JDK 25 with flags only - LEO CDP empirical result]]
- [[Baseline-diff gates must compare post-build to post-build when artifacts are committed]]
- [[Records break Gson pre-2.10, ArangoDB driver 6 VPACK, and handlebars getter resolution]]

%% ai-graph-end %%