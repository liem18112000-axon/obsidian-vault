---
title: "Read-side fire-and-forget mutation: pass the id and re-read in the async, don't mutate the object being serialized"
created: 2026-08-07
type: lesson
status: seedling
source: "luz_docs_import getImportJob timeout 2026-08-07"
tags: [concurrency, async, jakarta-ee, race-condition, design-decision]
---

# Read-side fire-and-forget mutation: pass the id and re-read in the async, don't mutate the object being serialized

When a read endpoint needs to trigger a side-effecting state change as a by-product (e.g. GET job → detect the job has timed out and persist status=FAILED), moving the *whole* check+update off the request thread into a fire-and-forget async worker should pass the **id**, not the just-read entity — and the async re-reads the entity itself.

Why re-read instead of handing the async the object the GET already loaded:
1. **Torn-read race.** The request thread returns that same object to the serializer (JAX-RS). If the async mutates its fields concurrently, the HTTP response can serialize a half-updated object.
2. **Lost-update / clobber.** The GET's snapshot may be stale by the time the async runs. Re-reading gets fresh state, so a job that legitimately reached a terminal state (DONE) in the gap is seen as terminal and skipped, instead of being overwritten with FAILED from the stale snapshot.

Cost: one extra read per call. Consequence to accept: the triggering response reflects the PRE-change state; the change only becomes visible on the next read (eventual, not read-your-write). That's the inherent trade-off of making the check fully asynchronous rather than computing it inline before returning.

Concretely in luz_docs_import: `DocsImportService.getImportJob` fires `DocsImportAsyncService.failJobIfStale(tenantId, token, id)` (@Asynchronous, TX NEVER); the async re-reads, checks isStale (non-terminal + lastModifield heartbeat older than 3600s, parse-safe), and only then flips to FAILED/TIMEOUT + updateJob. Note this is best-effort and still has a narrow TOCTOU between the async's read and its write — the durable fix is a conditional/CAS update (see the scheduled-sweeper proposal).

Related: [[luz_docs_import]], [[Durable-queue visibility timeout folds task-timeout and crash-resume into one mechanism]].

## Related

- [[luz_docs_import]]
