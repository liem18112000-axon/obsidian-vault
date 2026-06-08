---
title: "Binary-only vendored jars pin the API level of everything they link"
created: 2026-06-06
type: howto
status: seedling
source: "LEO CDP migration planning, session 2026-06-06"
tags: [java, jar, audit, vendored-deps, leo-cdp]
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
