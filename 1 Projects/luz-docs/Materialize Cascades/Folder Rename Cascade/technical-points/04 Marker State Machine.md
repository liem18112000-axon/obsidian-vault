---
ai_hash: c7d808f9bd4beee7
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Marker State Machine
- materializeCascade row
- claim transition
- retry
- PARTIAL
- START
- folder
- diagrams/03-marker-state-machine.png
- MaterializeRepository
- MaterializeFolderRenameService
- none
- deleted
- startCascadeMarker
- deleteCascadeMarker
- markCascadeMarkerPartial
- claimCascadeMarker
- HTTP 207
- uncaught exception
- MaterializeCascadeStatus
- Marker row
- State machine
- Claim
- Lock
- folder-rename-cascade
- 03 Cascade Attempt
- 05 Retry Flow
- status field
- markerId
- folderId
- cascade work
- job
- worker
- Transition
- lifecycle
- states
- rules
- database record
- model
- in progress
---

# Marker State Machine

[[folder-rename-cascade|Back to index]] | Previous: [[03 Cascade Attempt|Cascade attempt]] | Next: [[05 Retry Flow|Retry flow]]

The `materializeCascade` row has a tiny lifecycle. The important part is the **claim** transition: retry moves `PARTIAL` back to `START` before doing work, so another retry skips the same folder.

![[diagrams/03-marker-state-machine.png]]

## Transitions

Implemented in `MaterializeRepository` and `MaterializeFolderRenameService`.

| Transition | Meaning |
|---|---|
| none -> `START` | `startCascadeMarker(folderId)` on observer entry, when the event has `markerId == null`. This is a fresh cascade. |
| `START` -> deleted | `deleteCascadeMarker(markerId)` after the cascade fully succeeds. |
| `START` -> `PARTIAL` | `markCascadeMarkerPartial(markerId)` after HTTP 207. |
| `START` -> `START` | An uncaught exception leaves the marker untouched. Not a real transition, but useful to remember. |
| `PARTIAL` -> `START` | `claimCascadeMarker(markerId)` at the start of retry. This marks the work as in progress. |

`MaterializeCascadeStatus` is the enum that names these two states. Its `.name()` is stored in the `status` field.

## Beginner terms

| Term | Beginner explanation |
|---|---|
| Marker row | A small database record that remembers "there is cascade work in progress or needing retry." |
| State machine | A simple model where something can be in one state at a time and move between states by defined rules. |
| Enum | A fixed list of allowed values in code. Here: `START` and `PARTIAL`. |
| Claim | Marking a job as taken so another worker does not do the same job at the same time. |
| Lock | A way to prevent two workers from touching the same thing at once. This marker acts like a lightweight lock. |

%% ai-graph-start %%

**Related notes:**
- [[05 Retry Flow]]
- [[03 Cascade Attempt]]
- [[07 Operational Notes]]
- [[folder-rename-cascade]]
- [[09 Glossary for Newbies]]

**Relations:**
- Marker State Machine — *describes* — materializeCascade row lifecycle
- materializeCascade row — *has important part* — claim transition
- retry — *moves state from* — PARTIAL
- retry — *moves state to* — START
- retry — *skips* — folder
- diagrams/03-marker-state-machine.png — *illustrates* — Marker State Machine
- Transition — *implemented in* — MaterializeRepository
- Transition — *implemented in* — MaterializeFolderRenameService
- startCascadeMarker — *is a* — Transition
- deleteCascadeMarker — *is a* — Transition
- markCascadeMarkerPartial — *is a* — Transition
- claimCascadeMarker — *is a* — Transition
- startCascadeMarker — *causes transition from* — none
- startCascadeMarker — *causes transition to* — START
- startCascadeMarker — *uses parameter* — folderId
- startCascadeMarker — *used when event has* — markerId == null
- deleteCascadeMarker — *causes transition from* — START
- deleteCascadeMarker — *causes transition to* — deleted
- deleteCascadeMarker — *uses parameter* — markerId
- deleteCascadeMarker — *used after* — cascade fully succeeds
- markCascadeMarkerPartial — *causes transition from* — START
- markCascadeMarkerPartial — *causes transition to* — PARTIAL
- markCascadeMarkerPartial — *uses parameter* — markerId
- markCascadeMarkerPartial — *occurs after* — HTTP 207
- uncaught exception — *causes state to remain* — START
- claimCascadeMarker — *causes transition from* — PARTIAL
- claimCascadeMarker — *causes transition to* — START
- claimCascadeMarker — *uses parameter* — markerId
- claimCascadeMarker — *marks work as* — in progress
- MaterializeCascadeStatus — *is an enum for* — states
- MaterializeCascadeStatus — *names state* — START
- MaterializeCascadeStatus — *names state* — PARTIAL
- MaterializeCascadeStatus.name() — *is stored in* — status field
- Marker row — *is a* — database record
- Marker row — *remembers* — cascade work
- Marker row — *remembers* — needing retry
- State machine — *is a* — model
- State machine — *has* — states
- State machine — *has* — rules
- Claim — *is* — marking a job as taken
- Claim — *prevents* — another worker
- another worker — *from doing* — same job
- Lock — *prevents* — two workers
- two workers — *from touching* — same thing
- Marker row — *acts like a* — lightweight lock
- 04 Marker State Machine — *is part of* — folder-rename-cascade
- 04 Marker State Machine — *precedes* — 05 Retry Flow
- 04 Marker State Machine — *follows* — 03 Cascade Attempt

%% ai-graph-end %%