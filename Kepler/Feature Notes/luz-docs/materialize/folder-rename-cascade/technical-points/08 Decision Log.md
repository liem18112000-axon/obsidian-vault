# Decision Log

[[folder-rename-cascade|Back to index]] | Previous: [[07 Operational Notes|operational notes]] | Next: [[09 Glossary for Newbies|glossary]]

This records why the implementation has its current shape.

| Decision | Why | Alternatives rejected |
|---|---|---|
| Single server-side `updateMany` with `$map` | One round-trip, atomic per document, no skip pagination. | Skip pagination: page 2 timed out at 1000 documents because there is no `{folderIds, _id}` compound index and adding one cluster-wide was too much for this feature alone. |
| Mongo aggregation pipeline, not `bulkWrite` | MongoDB can compute the new `_folderNames` per document on the server. | Compute names client-side and send many `updateOne` calls; this was the previous broken approach. |
| Filter excludes already-correct rows with `$ne` on the slot | Keeps `matchedCount == modifiedCount`, so `luz_jsonstore` does not throw HTTP 207 for rows that were already correct. | Drop the strict equality check in `luz_jsonstore`; too risky because it affects other features. |
| Marker row written before the cascade | Survives a pod crash during cascade, so unfinished work is visible. | Write marker only after HTTP 207; that leaves a race where the pod can die after `updateMany` returns 207 but before the marker is written. |
| Status enum `START` / `PARTIAL`, not boolean | Retry can filter on `PARTIAL` and use `START` as an in-progress lock. | Single boolean `partial = true`; it cannot distinguish "in flight" from "needs retry." |
| Retry batch is 1 | Conservative: one folder at a time, no thundering herd. | Larger batch; postponed until stuck-folder accumulation is observed in production. |
| Retry uses cluster traffic, not a scheduled job | No new infrastructure or scheduler needed; busy tenants self-heal. | Quartz or Kubernetes CronJob; more overhead for an edge case. |

## Beginner terms

| Term | Beginner explanation |
|---|---|
| Decision log | A record of why technical choices were made. |
| Atomic per document | Each individual document update is applied as one safe unit. It does not mean all documents update in one all-or-nothing transaction. |
| Pagination | Splitting many records into pages, such as 1000 at a time. |
| Skip pagination | Pagination style that says "skip the first N records, then read the next page." It can get slow for large N. |
| Compound index | A database index over more than one field. |
| `bulkWrite` | MongoDB command for sending many write operations at once. |
| Race window | A small timing gap where something bad can happen if another event occurs at just the wrong moment. |
| Thundering herd | Too many workers starting heavy work at the same time. |
| Self-heal | The system fixes unfinished work later without a person manually starting it. |

