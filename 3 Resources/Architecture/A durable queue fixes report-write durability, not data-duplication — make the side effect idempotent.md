---
title: "A durable queue fixes report-write durability, not data-duplication — make the side effect idempotent"
created: 2026-08-10
type: lesson
status: seedling
source: "session 2026-08-10 (luz-docs-import fault-tolerance analysis)"
tags: [fault-tolerance, idempotency, pubsub, outbox, distributed-systems, design-decision]
---

# A durable queue fixes report-write durability, not data-duplication — make the side effect idempotent

When a worker does remote work (create documents) AND writes a progress/report record (job doc) via separate network calls, do not make the report the source of truth for idempotency. If a re-run reads the report to decide "already done", a lost report write causes DUPLICATE work.

**Split the two concerns:**
- **Report/status durability** — a durable at-least-once queue (PubSub) recovers a flaky report write, BUT only safely if: the write is an idempotent full-doc upsert; the message carries the full snapshot (not just an id); ordering/versioning uses a monotonic `rev` (NOT a wall-clock timestamp — clock skew/ties break it) or PubSub ordering keys; the guard is a server-side conditional write (`findAndModify rev<msg.rev`), not read-then-ack (TOCTOU); and 4xx goes to a DLQ+alert instead of infinite retry (only 5xx/timeout/429 are retryable).
- **Data/duplication correctness** — decouple from the report: derive "already done" from the actual durable ARTIFACT, and make the side effect IDEMPOTENT (deterministic id / upsert key, e.g. source+path+hash). Then replays/crashes/lost report writes can never duplicate — the report becomes a pure report whose worst failure is "status shows late," not "data is wrong."

**Rule of thumb:** a queue alone leaves a duplicate window (artifact created ✅ but report not yet written ❌ → crash → re-run duplicates). Idempotent creation closes it. If you can only do one, make the side effect idempotent first. Surfaced from the luz-docs-import fault-tolerance analysis. Related: [[luz-docs-import JobProgressWriter checkpoints are for crash-durability + heartbeat, not UI progress]], [[Luz docs-import zip flow: upload-zip returns job-id, poll GET until DONE]].

## Related

- [[luz-docs-import JobProgressWriter checkpoints are for crash-durability + heartbeat]]
- [[not UI progress]]
- [[Luz docs-import zip flow: upload-zip returns job-id]]
- [[poll GET until DONE]]
