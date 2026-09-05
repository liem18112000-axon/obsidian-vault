---
title: "Durable-queue visibility timeout folds task-timeout and crash-resume into one mechanism"
created: 2026-08-07
type: lesson
status: seedling
source: "luz_docs_import timeout proposal 2026-08-07"
tags: [messaging, pubsub, jms, idempotency, fault-tolerance, design-decision]
---

# Durable-queue visibility timeout folds task-timeout and crash-resume into one mechanism

For a long-running async task that today is fire-and-forget (e.g. a JEE `@Asynchronous` EJB), a bespoke **timeout detector** (scheduled sweeper / read-time staleness flip) only *marks* a stuck job failed — it never *resumes* the work. Moving the task onto a **durable broker** (Google Pub/Sub, JMS/Artemis) with **ack-on-complete + redeliver-on-crash** makes timeout **native**: the broker's visibility timeout / ack-deadline lapses when the worker dies, the message is redelivered (→ automatic resume), and a bounded `maxDeliveryAttempts` routes a poison task to a **dead-letter queue** whose handler marks it FAILED/TIMEOUT + notifies.

Key decision insight: the SAME mechanism delivers both the timeout AND crash-resume, so a durable queue **subsumes** the standalone timeout fix into the broader fault-tolerance roadmap — you don't build a separate sweeper if you're going to adopt the queue.

Non-negotiable prerequisite: **at-least-once redelivery requires an idempotent consumer.** Redelivery re-runs the task, so re-processing a partly-done unit must not duplicate side effects. In luz_docs_import this is already satisfied by IdempotentImportService (skip files already imported for a given importZipName) — that shipped idempotency is precisely the safety net that makes the queue approach safe.

Other real costs to budget for: (a) shared input storage — the redelivered consumer may be a different pod, so pod-local temp files won't do (move the input to GCS/object storage and pass a reference in the message); (b) a service/run-as token — a message redelivered hours later outlives the original user's JWT; (c) long-task lease extension — Pub/Sub ack-deadline maxes at 600s, so use StreamingPull auto-extension with maxAckExtensionPeriod >= worst-case duration, or checkpoint-and-redrive.

Related: [[luz_docs_import]], [[Truncating a JWT breaks signature verification and surfaces as 500 not 401]].

## Related

- [[luz_docs_import]]
