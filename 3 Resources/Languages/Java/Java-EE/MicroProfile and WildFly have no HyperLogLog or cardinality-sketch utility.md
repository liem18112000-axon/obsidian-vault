---
ai_hash: f3288e4240653310
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-09
entities: []
source: luz_docs docs/count-estimate/microprofile-wildfly-hyperloglog-libs.md, 2026-07-09
status: seedling
tags:
- microprofile
- wildfly
- infinispan
- hyperloglog
- java
title: MicroProfile and WildFly have no HyperLogLog or cardinality-sketch utility
type: observation
---

# MicroProfile and WildFly have no HyperLogLog or cardinality-sketch utility

Checked directly (source docs + web search, mid-2026): neither the MicroProfile umbrella spec (Config, Fault Tolerance, Health, JWT Auth, Metrics, OpenAPI, OpenTracing, Rest Client) nor WildFly itself ships a HyperLogLog or other cardinality-estimation sketch. MicroProfile Metrics has Counter/Gauge but those are exact observability counters, not probabilistic set-cardinality structures.

WildFly's own clustering layer (Infinispan) does provide clustered/distributed exact counters (CounterManager, strong/weak) via its "counters" capability — but that only activates under an HA or full-HA standalone profile (standalone-ha.xml / standalone-full-ha.xml) or domain-mode clustering. Infinispan itself has no HLL/cardinality-sketch data structure at all, clustered or not.

Practical implication: if a WildFly/MicroProfile app needs cardinality estimation, the answer is always a third-party Java library — Apache DataSketches (org.apache.datasketches:datasketches-java, actively maintained, Apache 2.0, recommended default) or historically stream-lib / java-hll (both lower-activity as of 2026). None of this is provided by the platform.

## Related

- [[3 Resources/Data/Algorithms/HyperLogLog cardinality estimation mechanism (hash, register, streak-length)]]

%% ai-graph-start %%

**Related notes:**
- [[luz_docs runs non-clustered WildFly pods, so pod-local sketchcounter state is broken]]
- [[Apache DataSketches datasketches-memory breaks on JDK 21 with NoClassDefFoundError]]
- [[HyperLogLog estimates distinct count in constant memory and is mergeable]]
- [[HyperLogLog cardinality estimation mechanism (hash, register, streak-length)]]

%% ai-graph-end %%