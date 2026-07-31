---
title: "Materialize migration per-doc failures leave no persisted failed-id record"
created: 2026-07-10
type: lesson
status: seedling
source: "luz_docs prod migration incident investigation, 2026-07-10"
tags: [luz-docs, migration, gotcha, logging]
---

# Materialize migration per-doc failures leave no persisted failed-id record

In luz_docs, `MaterializeMigrationExecutor.materialize()` catches per-document exceptions, logs a WARNING (with the full document JSON and stack trace), adds the doc id to a local `excludeIds` set for that run only, and moves on. Nothing about the failure is written to Mongo.

Because the document was never updated with the materialize fields, it still matches `MaterializeQueryBuilder.buildUnmaterializedFilter()` and gets picked back up on the next campaign attempt automatically — there is no separate "failed documents" collection or field to query later.

Practical implication: if you need to know which documents failed in a past run, the WARNING log lines are the *only* record — grep them by tenantId and timestamp window before they age out of log retention. There's nothing in the database to reconstruct that list after the fact.

Related: [[luz_docs migration campaigns retry on next tenant request, not cron]].
