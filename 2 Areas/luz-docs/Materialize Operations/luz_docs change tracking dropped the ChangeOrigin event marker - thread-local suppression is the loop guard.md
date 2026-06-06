---
title: "luz_docs change tracking dropped the ChangeOrigin event marker - thread-local suppression is the loop guard"
created: 2026-06-05
type: observation
status: budding
source: "JsonStoreChangeEvent refactor, session 2026-06-05"
tags: [luz-docs, change-tracking, design-decision, cdi-events]
---

# luz_docs change tracking dropped the ChangeOrigin event marker - thread-local suppression is the loop guard

The jsonstore change-tracking design originally gave `JsonStoreChangeEvent` an `origin` field (`API | OBSERVER`) as the second layer of its observer-loop guard. In practice that layer was implemented differently — observer-initiated sentinel writes run inside `ChangeTrackingSuppression.suppress()` (a thread-local, try-with-resources gate checked by `TrackingJsonStoreClient`), and sentinel fields are not tracked anyway — so `OBSERVER` was never constructed and `origin()` was never read. We removed the field and the enum (2026-06-05, decided with Liem): a designed-but-dead discriminator only costs comprehension, and it can be re-added if a writer ever needs to fire events while marked non-API (e.g. backfill jobs — still an open design question).

General lesson: when a loop guard ends up implemented as ambient context (thread-local suppression), an event-payload origin marker for the same purpose becomes dead weight — keep one mechanism, not both.

## Related

- [[luz_docs has two materialize cascade delivery mechanisms]]
- [[luz_docs materialize passive retry via cascade markers]]
