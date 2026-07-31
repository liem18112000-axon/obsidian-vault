---
title: "luz_docs runs non-clustered WildFly pods, so pod-local sketch/counter state is broken"
created: 2026-07-09
type: lesson
status: seedling
source: "verified via pom.xml, Dockerfile, kubernetes HPA config, 2026-07-09"
tags: [luz-docs, kepler, wildfly, architecture, gotcha]
---

# luz_docs runs non-clustered WildFly pods, so pod-local sketch/counter state is broken

Verified directly from luz_docs' pom.xml, Dockerfile, and its Kubernetes deployment: the app runs plain standalone.xml (not standalone-ha.xml / standalone-full-ha.xml), has no Infinispan or Redis dependency anywhere in source, and is deployed as a Kubernetes StatefulSet with an HPA scaling 1->10 replicas. So it is already multiple independent, non-clustered WildFly JVMs behind a load balancer, with zero shared in-memory state between pods today.

This matters for any design that wants to keep mutable aggregate state (a counter, a HyperLogLog sketch, an in-memory cache of "current count") in a CDI bean or any other pod-local JVM memory: with HPA at >1 replica, a read hitting pod A only ever reflects writes that happened to route through pod A. That is not a probabilistic-accuracy problem (unlike HLL's inherent error) -- it is a silent correctness bug, because the "missing" data from other pods never gets folded in at all.

The fix this codebase already uses for equivalent problems (_isPublic, _effectiveSecurityClassCodes, _shard) is to materialize the derived state into the document store (MongoDB, via the existing write-path/materialize pattern) instead of JVM memory, so every pod reads the same centrally-persisted value regardless of which pod produced it. Any new per-tenant/per-dimension aggregate (counter, HLL sketch, cache) on this app should follow the same rule: never trust pod-local memory to represent tenant-wide state when the app can scale beyond 1 replica.

See [[MicroProfile and WildFly have no HyperLogLog or cardinality-sketch utility]] and [[luz_docs count>N badge can use HyperLogLog with a fuzzy-zone fallback]].

## Related

- [[MicroProfile and WildFly have no HyperLogLog or cardinality-sketch utility]]
- [[luz_docs count>N badge can use HyperLogLog with a fuzzy-zone fallback]]
