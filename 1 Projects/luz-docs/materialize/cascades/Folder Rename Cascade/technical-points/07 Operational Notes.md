---
ai_hash: 6a63ea57effe2e08
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Operational Notes
- folder-rename-cascade
- 06 Files of Record
- 08 Decision Log
- materializeCascade
- tenant database
- documents
- folders
- central queue
- Cascade
- retry
- tenants
- luz.docs.tenants.use-materialized
- filter
- folderIds
- _folderNames
- materialize index
- START row
- PARTIAL row
- crashed pod
- system
- allowlisted request
- CASCADE_RETRY_BATCH
- stuck folders
- Operations
- N minutes
- manual investigation
- deletion
- promotion
- production systems
- Pod
- Kubernetes
- app containers
- app instance
- Queue
- work items
- tenant collections
- Throughput
- marker
- feature
---

# Operational Notes

[[folder-rename-cascade|Back to index]] | Previous: [[06 Files of Record|files of record]] | Next: [[08 Decision Log|decision log]]

## Notes for running and supporting this feature

| Topic | Note | Beginner explanation |
|---|---|---|
| Per-tenant collection | `materializeCascade` lives inside each tenant database, beside `documents` and `folders`. There is no central queue. | Each customer keeps its own retry records. |
| Allowlist gate | Cascade and retry only run for tenants in `luz.docs.tenants.use-materialized`. | Only selected tenants use this feature. |
| Indexing | The filter hits `{ folderIds, _folderNames }`. The existing `_folderNames` materialize index covers this; no new index was added. | An index is like a book index: it helps the database find matching records faster. |
| Pod restart safety | A `START` row left by a crashed pod is not picked up by retry because retry only reads `PARTIAL`. | The system avoids automatically repeating work that may have failed for a serious reason. |
| Throughput | One allowlisted request drains one `PARTIAL` row because `CASCADE_RETRY_BATCH = 1`. | A tenant with 100 stuck folders needs 100 qualifying requests unless the batch size is increased. |

## Manual audit reminder

Operations should periodically check `materializeCascade` for rows older than N minutes that are still in `START`.

Those rows may need manual investigation, deletion, or promotion depending on the failure.

## Beginner terms

| Term | Beginner explanation |
|---|---|
| Operations / Ops | The people or process responsible for keeping production systems healthy. |
| Pod | A Kubernetes unit that runs one or more app containers. In casual terms: one running copy of the app. |
| Restart safety | Behavior after a running app instance crashes or restarts. |
| Queue | A list of work items waiting to be processed. This feature uses tenant collections instead of a central queue. |
| Throughput | How much work the system can process in a period of time. |
| Promote a marker | Manually changing a marker so retry can pick it up again, for example from `START` to `PARTIAL`. |

%% ai-graph-start %%

**Related notes:**
- [[folder-rename-cascade]]
- [[05 Retry Flow]]
- [[03 Cascade Attempt]]
- [[08 Decision Log]]
- [[06 Files of Record]]

**Relations:**
- Operational Notes — *is part of* — folder-rename-cascade
- Operational Notes — *follows* — 06 Files of Record
- Operational Notes — *precedes* — 08 Decision Log
- Operational Notes — *describes* — feature
- materializeCascade — *lives inside* — tenant database
- tenant database — *contains* — documents
- tenant database — *contains* — folders
- feature — *does not use* — central queue
- feature — *uses* — tenant collections
- Cascade — *runs for* — tenants
- retry — *runs for* — tenants
- tenants — *are in* — luz.docs.tenants.use-materialized
- filter — *hits* — folderIds
- filter — *hits* — _folderNames
- _folderNames — *is covered by* — materialize index
- START row — *is left by* — crashed pod
- retry — *does not pick up* — START row
- retry — *reads* — PARTIAL row
- system — *avoids repeating* — work items
- allowlisted request — *drains* — PARTIAL row
- CASCADE_RETRY_BATCH — *equals* — 1
- stuck folders — *require* — allowlisted request
- Operations — *checks* — materializeCascade
- materializeCascade — *contains* — START row
- START row — *may need* — manual investigation
- START row — *may need* — deletion
- START row — *may need* — promotion
- Operations — *are responsible for* — production systems
- Pod — *is a unit of* — Kubernetes
- Pod — *runs* — app containers
- Pod — *is a* — app instance
- Restart safety — *describes behavior after* — app instance
- app instance — *crashes* — 
- app instance — *restarts* — 
- Queue — *is a list of* — work items
- Throughput — *measures* — work items
- Promote a marker — *changes* — marker
- marker — *changes from* — START row
- marker — *changes to* — PARTIAL row
- retry — *picks up* — marker

%% ai-graph-end %%