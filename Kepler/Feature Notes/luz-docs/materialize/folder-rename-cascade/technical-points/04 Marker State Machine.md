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

