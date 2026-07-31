---
title: "ManagedExecutorService.execute loses CDI request context"
created: 2026-07-23
type: lesson
status: seedling
source: "session 2026-07-23 MaterializeGate stampede panel"
tags: [cdi, java-ee, async, gotcha, luz-docs]
---

# ManagedExecutorService.execute loses CDI request context

Submitting work to a bare `ManagedExecutorService.execute()` (or any raw executor) in Java EE/Jakarta gives the task **no active CDI request context** — the first call into a `@RequestScoped` bean throws `ContextNotActiveException`. If the calling code swallows RuntimeExceptions (catch-and-log guard helpers), the failure is **silent**: the async job "runs", produces a wrong default, and caches/persists it.

Fix that keeps the same executor: fire a CDI async event instead — `Event.fireAsync(payload, NotificationOptions.ofExecutor(executor))` observed with `@ObservesAsync`. CDI 2.0 §6.7 requires the container to activate a request context for async observer notification, so `@RequestScoped` beans work inside the observer.

Why it is sneaky: bean chains that are fully `@ApplicationScoped` work fine off-thread, so tests that only exercise those paths pass. In luz_docs, `MigrationCampaignService` (app-scoped REST client) works from a raw executor but `JsonStoreMongoService` (`@RequestScoped`) does not — a stale-while-revalidate gate design would have silently cached INCOMPLETE forever for exactly the tenants relying on the count fall-through, while looking healthy on tenants with campaign records.

Rule: async work touching `@RequestScoped` beans goes through `fireAsync`/`@ObservesAsync` (the shipped LUZ-157705 pattern), never raw `executor.execute`.

## Related

- [[Per-pod single-flight kills cache stampede without semantic change]]
