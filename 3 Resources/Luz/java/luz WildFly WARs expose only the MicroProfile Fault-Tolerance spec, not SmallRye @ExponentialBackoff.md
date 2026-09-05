---
title: "luz WildFly WARs expose only the MicroProfile Fault-Tolerance spec, not SmallRye @ExponentialBackoff"
created: 2026-08-10
type: lesson
status: seedling
source: "LUZ-158230 session 2026-08-10"
tags: [luz, microprofile, fault-tolerance, wildfly, gotcha, maven]
---

# luz WildFly WARs expose only the MicroProfile Fault-Tolerance spec, not SmallRye @ExponentialBackoff

luz WildFly-26 WARs (base image `luz-wildfly26-all:26.1.2.Final-5`, e.g. `luz_docs`, `luz_docs_import`) have only the **MicroProfile Fault-Tolerance _spec_ API** (`org.eclipse.microprofile.faulttolerance`) on the compile classpath — **not** SmallRye's `io.smallrye.faulttolerance.api`. So `@ExponentialBackoff` does not compile there. It IS available on **Quarkus** modules (e.g. `luz_storage_batch`, via `quarkus-smallrye-fault-tolerance`).

**Why it bites:** a jar sitting in `~/.m2` does NOT mean it is on a given module's classpath. Confirm with `mvn compile`, never by locating the jar.

**Workarounds:**
- Jittered fixed-delay retries without exponential: spec `@Retry(... jitter = 1, jitterDelayUnit = ChronoUnit.SECONDS)`.
- To get true exponential backoff, add `io.smallrye:smallrye-fault-tolerance-api` as a `provided` dependency at the version the WildFly image bundles, then use `@ExponentialBackoff`.

Surfaced on LUZ-158230 (import-job Tier 1 terminal-write hardening) in `luz_docs_import`.

## Related

- [[Hosting a Pub/Sub consumer in a luz service with luz_message_receiver]]
- [[Align the Google Cloud stack in a luz WildFly WAR via libraries-bom]]
