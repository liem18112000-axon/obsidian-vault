---
title: "a2a-sdk DatabaseTaskStore makes A2A tasks survive Cloud Run restarts"
created: 2026-08-29
type: lesson
status: seedling
source: "session 2026-08-29 task-store migration"
tags: [a2a, a2a-sdk, task-store, cloud-run, postgres]
---

# a2a-sdk DatabaseTaskStore makes A2A tasks survive Cloud Run restarts

The a2a-sdk `DefaultRequestHandler` persists A2A `Task` objects through a `TaskStore`. The default `InMemoryTaskStore` keeps them in a process dict, so **every task is lost when the Cloud Run instance restarts, scales to zero, or gets a new revision**, and tasks are invisible to other instances (a follow-up `tasks/get` landing on a different instance returns `None`). That is only safe pinned to one always-warm instance.

`DatabaseTaskStore(engine, create_table=True)` (from `a2a-sdk[postgresql]`, which pulls `sqlalchemy[asyncio,postgresql-asyncpg]`) makes tasks durable and shareable across instances. Key behaviors:

- It takes an **already-built** `AsyncEngine` (you call `create_async_engine` yourself).
- **Lazy init**: it runs `CREATE TABLE` on first use via `_ensure_initialized`, so there is no startup `await` to wire — constructing it is synchronous and cheap.
- Tasks are keyed by `owner` (from `owner_resolver`, default `resolve_user_scope`) then task id (UUID). With a shared bearer and no per-user identity, everything lands under one owner; two agents sharing one DB can share the `tasks` table safely because ids are UUIDs.

Pattern used: a `build_task_store()` factory returns `DatabaseTaskStore` when DB env is set, else `InMemoryTaskStore`, so local dev/tests need no database. Distinct from any app-domain state (e.g. a GCS memory bank) — this store is only A2A protocol task bookkeeping.

Connect on Cloud Run via [[Cloud Run to Cloud SQL via Auth-proxy unix socket with asyncpg]].

## Related

- [[Cloud Run to Cloud SQL via Auth-proxy unix socket with asyncpg]]
