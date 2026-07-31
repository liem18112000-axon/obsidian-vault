---
title: "Async CDI observers must receive the session token via the event payload"
created: 2026-07-21
type: lesson
status: seedling
source: "session 2026-07-21, LUZ-157705"
tags: [java, cdi, async, jakarta-ee, luz-docs, gotcha]
---

# Async CDI observers must receive the session token via the event payload

When work is moved off the request thread with `Event.fireAsync`, the `@ObservesAsync` observer runs in a fresh CDI request context — request-scoped producers like `@CurrentSession Token` do not carry over and may inject null or fail there. The session token (and tenant id) must travel inside the event payload record instead.

luz-docs convention: event records carry `Token` explicitly (MaterializeFolderRenameEvent, MigrationEvent carries the jwt string, MaterializeCampaignCheckEvent/ParallelizeCampaignCheckEvent), the firing side lives in the request scope where `@CurrentSession` is valid, and the observer is `@ApplicationScoped` and reads everything from `event.token()` / `event.tenantId()`.

Fire with an explicit executor — `fireAsync(event, NotificationOptions.ofExecutor(managedExecutorService))` — and wrap the fire call in try/catch: a failed dispatch must never break the request that triggered it.

## Related

- [[Campaign COMPLETED status is only trusted after re-verifying document state (truth-check gate)]]
