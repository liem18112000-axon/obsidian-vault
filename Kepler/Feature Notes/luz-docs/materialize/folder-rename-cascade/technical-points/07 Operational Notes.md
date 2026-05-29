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

