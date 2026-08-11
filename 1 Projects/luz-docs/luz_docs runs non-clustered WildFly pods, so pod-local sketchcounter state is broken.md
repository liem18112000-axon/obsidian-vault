---
ai_hash: 854a796e36363ceb
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-09
entities:
- luz_docs
- WildFly pods
- pod-local sketch/counter state
- pom.xml
- Dockerfile
- Kubernetes deployment
- standalone.xml
- standalone-ha.xml
- standalone-full-ha.xml
- Infinispan
- Redis
- Kubernetes StatefulSet
- HPA
- load balancer
- shared in-memory state
- mutable aggregate state
- counter
- HyperLogLog sketch
- in-memory cache
- CDI bean
- pod-local JVM memory
- pod A
- probabilistic-accuracy problem
- silent correctness bug
- _isPublic
- _effectiveSecurityClassCodes
- _shard
- document store
- MongoDB
- write-path/materialize pattern
- centrally-persisted value
- tenant-wide state
- MicroProfile
- cardinality-sketch utility
- luz_docs countN badge
- fuzzy-zone fallback
- replicas
- missing data
source: verified via pom.xml, Dockerfile, kubernetes HPA config, 2026-07-09
status: seedling
tags:
- luz-docs
- kepler
- wildfly
- architecture
- gotcha
title: luz_docs runs non-clustered WildFly pods, so pod-local sketch/counter state
  is broken
type: lesson
---

# luz_docs runs non-clustered WildFly pods, so pod-local sketch/counter state is broken

Verified directly from luz_docs' pom.xml, Dockerfile, and its Kubernetes deployment: the app runs plain standalone.xml (not standalone-ha.xml / standalone-full-ha.xml), has no Infinispan or Redis dependency anywhere in source, and is deployed as a Kubernetes StatefulSet with an HPA scaling 1->10 replicas. So it is already multiple independent, non-clustered WildFly JVMs behind a load balancer, with zero shared in-memory state between pods today.

This matters for any design that wants to keep mutable aggregate state (a counter, a HyperLogLog sketch, an in-memory cache of "current count") in a CDI bean or any other pod-local JVM memory: with HPA at >1 replica, a read hitting pod A only ever reflects writes that happened to route through pod A. That is not a probabilistic-accuracy problem (unlike HLL's inherent error) -- it is a silent correctness bug, because the "missing" data from other pods never gets folded in at all.

The fix this codebase already uses for equivalent problems (_isPublic, _effectiveSecurityClassCodes, _shard) is to materialize the derived state into the document store (MongoDB, via the existing write-path/materialize pattern) instead of JVM memory, so every pod reads the same centrally-persisted value regardless of which pod produced it. Any new per-tenant/per-dimension aggregate (counter, HLL sketch, cache) on this app should follow the same rule: never trust pod-local memory to represent tenant-wide state when the app can scale beyond 1 replica.

See [[MicroProfile and WildFly have no HyperLogLog or cardinality-sketch utility]] and [[1 Projects/luz-docs/luz_docs countN badge can use HyperLogLog with a fuzzy-zone fallback]].

## Related

- [[MicroProfile and WildFly have no HyperLogLog or cardinality-sketch utility]]
- [[1 Projects/luz-docs/luz_docs countN badge can use HyperLogLog with a fuzzy-zone fallback]]

%% ai-graph-start %%

**Related notes:**
- [[MicroProfile and WildFly have no HyperLogLog or cardinality-sketch utility]]
- [[luz_docs estimated-count POC drops CAS and backfill gate]]
- [[luz_docs countN badge can use HyperLogLog with a fuzzy-zone fallback]]
- [[luz_docs documentscount is scan-bound and cannot reach sub-second at 128k]]
- [[Apache DataSketches datasketches-memory breaks on JDK 21 with NoClassDefFoundError]]

**Relations:**
- luz_docs — *runs* — WildFly pods
- WildFly pods — *are* — non-clustered
- pod-local sketch/counter state — *is* — broken
- non-clustered WildFly pods — *cause* — pod-local sketch/counter state
- luz_docs — *uses* — pom.xml
- luz_docs — *uses* — Dockerfile
- luz_docs — *uses* — Kubernetes deployment
- luz_docs — *runs* — standalone.xml
- luz_docs — *does not run* — standalone-ha.xml
- luz_docs — *does not run* — standalone-full-ha.xml
- luz_docs — *has no dependency on* — Infinispan
- luz_docs — *has no dependency on* — Redis
- luz_docs — *is deployed as* — Kubernetes StatefulSet
- Kubernetes StatefulSet — *uses* — HPA
- HPA — *scales* — replicas
- HPA — *scales* — 1->10 replicas
- WildFly pods — *are behind* — load balancer
- WildFly pods — *have* — zero shared in-memory state
- mutable aggregate state — *includes* — counter
- mutable aggregate state — *includes* — HyperLogLog sketch
- mutable aggregate state — *includes* — in-memory cache
- mutable aggregate state — *can be stored in* — CDI bean
- mutable aggregate state — *can be stored in* — pod-local JVM memory
- pod A — *reflects writes from* — pod A
- missing data — *from other pods never gets folded in* — null
- HyperLogLog sketch — *has* — inherent error
- silent correctness bug — *is not* — probabilistic-accuracy problem
- _isPublic — *is a fix for* — equivalent problems
- _effectiveSecurityClassCodes — *is a fix for* — equivalent problems
- _shard — *is a fix for* — equivalent problems
- fix — *materializes state into* — document store
- document store — *is* — MongoDB
- MongoDB — *uses* — write-path/materialize pattern
- centrally-persisted value — *is read by* — WildFly pods
- MicroProfile — *has no utility for* — HyperLogLog sketch
- MicroProfile — *has no utility for* — cardinality-sketch utility
- WildFly — *has no utility for* — HyperLogLog sketch
- WildFly — *has no utility for* — cardinality-sketch utility
- luz_docs countN badge — *can use* — HyperLogLog sketch
- HyperLogLog sketch — *can use* — fuzzy-zone fallback
- luz_docs — *should not trust* — pod-local JVM memory
- pod-local JVM memory — *for* — tenant-wide state
- luz_docs — *has* — existing write-path/materialize pattern

%% ai-graph-end %%