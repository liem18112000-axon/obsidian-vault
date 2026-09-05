---
title: "MicroProfile @Retry can't do exponential backoff or HTTP-status-aware retry — use a manual loop"
created: 2026-08-10
type: howto
status: seedling
source: "session 2026-08-10 (luz-docs-import Tier 1 terminal write)"
tags: [java, microprofile, fault-tolerance, retry, backoff, jax-rs]
---

# MicroProfile @Retry can't do exponential backoff or HTTP-status-aware retry — use a manual loop

MicroProfile Fault Tolerance `@Retry` does fixed-delay + optional `jitter` only — it has NO exponential backoff and cannot branch on HTTP status (it matches on exception TYPE via retryOn/abortOn). SmallRye adds a non-spec `@ExponentialBackoff`, but only if `io.smallrye.faulttolerance:smallrye-fault-tolerance-api` is on the classpath (often it is not).

When you need exponential backoff AND "retry 5xx/timeout but abort 4xx", write a manual retry loop instead of the annotation:
- catch `javax.ws.rs.WebApplicationException`; `e.getResponse().getStatus()` gives the HTTP code. 400-499 → abort (a rejected request will not succeed on retry). 500-599 → retry.
- Timeouts/connection errors surface as `javax.ws.rs.ProcessingException` (NOT a WebApplicationException) → generic catch → retry.
- Backoff = equal-jitter: `half = min(cap, base*2^(n-1))/2; sleep = half + rand(0,half)` (spreads retries, avoids thundering herd). `ThreadLocalRandom` for the jitter.
- On final give-up of a NON-self-healing write, do not swallow: emit a SEVERE structured alert with a stable marker (e.g. `RECONCILE_NEEDED jobId=...`) so a log-based alert / reconciler can repair it.

MP `@Retry` self-invocation caveat: interceptors fire only through the CDI/EJB proxy, so two annotated public methods can share a plain (un-annotated) private helper for the actual work — the interceptor on the public entry still wraps the private call. Applied in luz-docs-import `JsonStoreService.updateJobFinal`. Related: [[A durable queue fixes report-write durability, not data-duplication — make the side effect idempotent]].

## Related

- [[A durable queue fixes report-write durability]]
- [[not data-duplication — make the side effect idempotent]]

## Correction (Klara platform)

Spec `@Retry` still can't do exponential backoff alone, but a MANUAL LOOP is the WRONG fix on the Klara luz platform: WildFly 26 + SmallRye Fault Tolerance, and smallrye-fault-tolerance-api IS on the classpath (luz_storage_batch uses @ExponentialBackoff). Prefer declarative: @Retry(retryOn={ServerErrorException,ProcessingException}, abortOn=ClientErrorException, jitter, maxDuration) + @ExponentialBackoff + @Fallback. The 4xx-vs-5xx branch is expressible by EXCEPTION TYPE (JAX-RS: 4xx->ClientErrorException, 5xx->ServerErrorException, timeout/conn->ProcessingException). Applied in luz-docs-import JsonStoreService.updateJobFinal. Only hand-roll a loop if the SmallRye api jar is genuinely absent.

## Correction 2 (runtime split — verified by compile)

The first correction was too broad. `@ExponentialBackoff` availability depends on the RUNTIME, not the org:
- **Quarkus modules** (e.g. luz_storage_batch via `quarkus-smallrye-fault-tolerance`) bundle the SmallRye api on the COMPILE classpath -> `@ExponentialBackoff` compiles.
- **WildFly WARs** (e.g. luz_docs_import) carry ONLY the MicroProfile FT SPEC api (`org.eclipse.microprofile.faulttolerance`) on the compile classpath. `import io.smallrye.faulttolerance.api.ExponentialBackoff` FAILS to compile: `package io.smallrye.faulttolerance.api does not exist`. The jar being in `.m2` does NOT put it on this module's classpath.
To use `@ExponentialBackoff` in a WildFly WAR you must add `io.smallrye:smallrye-fault-tolerance-api` as a `provided` dep at the version the WildFly image bundles (runtime-honoring risk if mismatched). Otherwise use spec `@Retry` + `jitter` (jittered fixed delay, not exponential) — that is what luz-docs-import JsonStoreService.updateJobFinal ships. Lesson: verify a compile classpath with an actual `mvn compile`, not by finding the jar in `.m2`.
