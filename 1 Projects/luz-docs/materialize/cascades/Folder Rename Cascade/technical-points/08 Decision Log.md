---
ai_hash: ce4d4133eaa025cc
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Decision Log
- folder-rename-cascade
- 07 Operational Notes
- 09 Glossary for Newbies
- Implementation Shape
- Single server-side `updateMany` with `$map`
- One round-trip
- Atomic per document
- Skip pagination
- Page 2 timeout
- 1000 documents
- '`{folderIds, _id}` compound index'
- Feature
- Mongo aggregation pipeline
- '`bulkWrite`'
- MongoDB
- '`_folderNames`'
- Server-side computation
- Client-side computation
- '`updateOne` calls'
- Previous broken approach
- Filter excludes already-correct rows with `$ne` on the slot
- '`matchedCount == modifiedCount`'
- '`luz_jsonstore`'
- HTTP 207
- Already correct rows
- Strict equality check
- Other features
- Marker row written before the cascade
- Pod crash
- Cascade
- Unfinished work
- Write marker only after HTTP 207
- Race window
- '`updateMany` returns 207'
- Status enum `START` / `PARTIAL`
- Boolean status
- Retry mechanism
- '`PARTIAL` status'
- '`START` status'
- In-progress lock
- In flight status
- Needs retry status
- Retry batch size 1
- One folder at a time
- Thundering herd
- Larger batch size
- Stuck-folder accumulation
- Production environment
- Retry uses cluster traffic
- Scheduled job
- Infrastructure
- Scheduler
- Busy tenants
- Self-healing system
- Quartz
- Kubernetes CronJob
- Overhead
- Edge case
- Technical choices
- Individual document update
- Pagination
- Database index
- More than one field
- Many write operations
- Timing gap
- Something bad
- Many workers
- Heavy work
- All-or-nothing transaction
- Large N
- System
- record
- splitting many records into pages
- Pagination style
- safe unit
- MongoDB command
---

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

%% ai-graph-start %%

**Related notes:**
- [[07 Operational Notes]]
- [[03 Cascade Attempt]]
- [[folder-rename-cascade]]
- [[09 Glossary for Newbies]]
- [[02 Trigger Flow]]

**Relations:**
- Decision Log — *records* — Implementation Shape
- Decision Log — *explains* — Technical choices
- folder-rename-cascade — *is_index_for* — Decision Log
- 07 Operational Notes — *is_previous_note_for* — Decision Log
- 09 Glossary for Newbies — *is_next_note_for* — Decision Log
- Single server-side `updateMany` with `$map` — *is_decision_for* — Implementation Shape
- Single server-side `updateMany` with `$map` — *achieves* — One round-trip
- Single server-side `updateMany` with `$map` — *achieves* — Atomic per document
- Single server-side `updateMany` with `$map` — *avoids* — Skip pagination
- Skip pagination — *rejected_due_to* — Page 2 timeout
- Page 2 timeout — *occurred_at* — 1000 documents
- Page 2 timeout — *caused_by_lack_of* — `{folderIds, _id}` compound index
- Adding `{folderIds, _id}` compound index — *was_too_much_for* — Feature
- Mongo aggregation pipeline — *is_decision_for* — Implementation Shape
- Mongo aggregation pipeline — *allows* — MongoDB
- MongoDB — *to_compute* — `_folderNames`
- MongoDB — *on* — Server-side computation
- Mongo aggregation pipeline — *rejected* — Client-side computation
- Client-side computation — *uses* — `updateOne` calls
- Client-side computation — *was* — Previous broken approach
- Filter excludes already-correct rows with `$ne` on the slot — *is_decision_for* — Implementation Shape
- Filter excludes already-correct rows with `$ne` on the slot — *maintains* — `matchedCount == modifiedCount`
- Filter excludes already-correct rows with `$ne` on the slot — *prevents* — `luz_jsonstore`
- `luz_jsonstore` — *from_throwing* — HTTP 207
- HTTP 207 — *for* — Already correct rows
- Drop strict equality check — *rejected_due_to* — too risky
- Drop strict equality check — *affects* — Other features
- Marker row written before the cascade — *is_decision_for* — Implementation Shape
- Marker row written before the cascade — *survives* — Pod crash
- Pod crash — *during* — Cascade
- Marker row written before the cascade — *makes_visible* — Unfinished work
- Write marker only after HTTP 207 — *rejected_due_to* — Race window
- Race window — *occurs_if* — Pod crash
- Pod crash — *after* — `updateMany` returns 207
- Pod crash — *before* — marker is written
- Status enum `START` / `PARTIAL` — *is_decision_for* — Implementation Shape
- Status enum `START` / `PARTIAL` — *enables* — Retry mechanism
- Retry mechanism — *to_filter_on* — `PARTIAL` status
- Retry mechanism — *to_use* — `START` status
- `START` status — *as* — In-progress lock
- Boolean status — *rejected_because* — cannot distinguish
- cannot distinguish — *between* — In flight status
- cannot distinguish — *from* — Needs retry status
- Retry batch size 1 — *is_decision_for* — Implementation Shape
- Retry batch size 1 — *processes* — One folder at a time
- Retry batch size 1 — *avoids* — Thundering herd
- Larger batch size — *postponed_until* — Stuck-folder accumulation
- Stuck-folder accumulation — *observed_in* — Production environment
- Retry uses cluster traffic — *is_decision_for* — Implementation Shape
- Retry uses cluster traffic — *requires* — no new Infrastructure
- Retry uses cluster traffic — *requires* — no new Scheduler
- Retry uses cluster traffic — *enables* — Busy tenants
- Busy tenants — *to* — Self-healing system
- Scheduled job — *rejected_due_to* — Overhead
- Scheduled job — *includes* — Quartz
- Scheduled job — *includes* — Kubernetes CronJob
- Scheduled job — *is_for* — Edge case
- Decision log (term) — *explains* — Decision Log
- Decision log (term) — *is_a* — record
- record — *of* — Technical choices
- Atomic per document (term) — *explains* — Atomic per document
- Atomic per document (term) — *means* — Individual document update
- Individual document update — *is_a* — safe unit
- Atomic per document (term) — *does_not_mean* — All-or-nothing transaction
- Pagination (term) — *explains* — Pagination
- Pagination (term) — *is* — splitting many records into pages
- Skip pagination (term) — *explains* — Skip pagination
- Skip pagination (term) — *is_a* — Pagination style
- Skip pagination (term) — *can_be* — slow
- slow — *for* — Large N
- Compound index (term) — *explains* — `{folderIds, _id}` compound index
- Compound index (term) — *is_a* — Database index
- Database index — *over* — More than one field
- `bulkWrite` (term) — *explains* — `bulkWrite`
- `bulkWrite` (term) — *is_a* — MongoDB command
- MongoDB command — *for* — Many write operations
- Race window (term) — *explains* — Race window
- Race window (term) — *is_a* — Timing gap
- Timing gap — *where* — Something bad can happen
- Thundering herd (term) — *explains* — Thundering herd
- Thundering herd (term) — *is* — Many workers starting heavy work at the same time
- Self-heal (term) — *explains* — Self-healing system
- Self-heal (term) — *means* — System fixes unfinished work later

%% ai-graph-end %%