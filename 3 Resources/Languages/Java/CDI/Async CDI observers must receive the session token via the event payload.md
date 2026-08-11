---
ai_hash: 9f40236c425126e0
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-21
entities: []
source: session 2026-07-21, LUZ-157705
status: seedling
tags:
- java
- cdi
- async
- jakarta-ee
- luz-docs
- gotcha
title: Async CDI observers must receive the session token via the event payload
type: lesson
---

# Async CDI observers must receive the session token via the event payload

When work is moved off the request thread with `Event.fireAsync`, the `@ObservesAsync` observer runs in a fresh CDI request context — request-scoped producers like `@CurrentSession Token` do not carry over and may inject null or fail there. The session token (and tenant id) must travel inside the event payload record instead.

luz-docs convention: event records carry `Token` explicitly (MaterializeFolderRenameEvent, MigrationEvent carries the jwt string, MaterializeCampaignCheckEvent/ParallelizeCampaignCheckEvent), the firing side lives in the request scope where `@CurrentSession` is valid, and the observer is `@ApplicationScoped` and reads everything from `event.token()` / `event.tenantId()`.

Fire with an explicit executor — `fireAsync(event, NotificationOptions.ofExecutor(managedExecutorService))` — and wrap the fire call in try/catch: a failed dispatch must never break the request that triggered it.

## Related

- [[Campaign COMPLETED status is only trusted after re-verifying document state (truth-check gate)]]

%% ai-graph-start %%

**Related notes:**
- [[Campaign COMPLETED status is only trusted after re-verifying document state (truth-check gate)]]
- [[luz-docs migration campaign per-tenant activation flow]]
- [[Context-propagating fireAsync before the resource method wipes JAX-RS @Context proxies (RESTEASY003880)]]
- [[ManagedExecutorService.execute loses CDI request context]]
- [[CDI observer methods are inherited from superclasses but not across packages if package-private]]

%% ai-graph-end %%