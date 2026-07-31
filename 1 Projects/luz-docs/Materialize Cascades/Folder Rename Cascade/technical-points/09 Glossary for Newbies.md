---
ai_hash: 9749561eed9e7d7d
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Backend
- Application
- API
- Software systems
- Endpoint
- Folder ID
- Folder
- Folder name
- Cascade
- Materialized data
- Tenant
- MongoDB
- Collection
- SQL databases
- Document
- Field
- Array
- Filter
- '`updateMany`'
- Aggregation pipeline
- Index
- Database
- Records
- Service
- Business logic
- Repository
- Facade
- Logic
- Event
- Observer
- Async
- Background work
- Executor
- Exception
- Java
- Retry
- Marker
- Lock
- Partial
- Timeout
- Idempotent
- Race condition
- Race window
- Manual cleanup
- Operations script
- Person
- Unfinished work
- Failed work
- Simultaneous work
- Normal flow
- '`_folderNames`'
- Table
- Unfinished cascade work
---

# Glossary for Newbies

[[folder-rename-cascade|Back to index]]

This glossary explains technical terms from the folder rename cascade notes in beginner-friendly language.

## Core concepts

| Term | Beginner explanation |
|---|---|
| Backend | The server-side part of an application. Users usually do not see it directly. |
| API | A way for software systems to talk to each other. Example: `PUT /folders/{id}` asks the backend to update a folder. |
| Endpoint | One specific API address and action, such as `PATCH /folders/{id}`. |
| Folder ID | A stable internal identifier for a folder. It usually does not change when the folder is renamed. |
| Folder name | The human-readable label shown to users. This can change. |
| Cascade | A chain reaction where one change causes related updates elsewhere. |
| Materialized data | Copied or precomputed data stored to make reads faster. It must be kept synchronized. |
| Tenant | One customer or organization using the system, with data separated from others. |

## MongoDB terms

| Term | Beginner explanation |
|---|---|
| MongoDB | A database that stores records as flexible JSON-like documents. |
| Collection | A group of MongoDB documents, similar to a table in SQL databases. |
| Document | A JSON-like record stored in MongoDB. |
| Field | A named value inside a document, like `folderIds` or `_folderNames`. |
| Array | A list of values, such as `["Inbox", "Legal"]`. |
| Filter | Rules that choose which documents should be affected. |
| `updateMany` | A MongoDB command that updates all documents matching a filter. |
| Aggregation pipeline | A list of MongoDB processing steps. In this feature it is used to compute the new `_folderNames` array. |
| Index | A helper structure that lets the database find records faster. Like an index at the back of a book. |

## Java and service terms

| Term | Beginner explanation |
|---|---|
| Service | A class or component that owns a piece of business logic. |
| Repository | A class that talks to the database. |
| Facade | A simpler front door to more detailed logic behind it. |
| Event | A message inside the app saying something happened. |
| Observer | Code that listens for an event and reacts. |
| Async | Short for asynchronous: the work happens in the background instead of blocking the current request. |
| Executor | A component that runs background work. |
| Exception | An error object in Java. Throwing an exception usually means the normal flow could not continue. |

## Reliability terms

| Term | Beginner explanation |
|---|---|
| Retry | Trying unfinished or failed work again later. |
| Marker | A small database record that remembers unfinished cascade work. |
| Lock | A way to prevent two workers from doing the same work at the same time. |
| Partial | Only some of the intended work finished. |
| Timeout | The system waited too long for a response and stopped waiting. |
| Idempotent | Safe to run multiple times without causing extra unwanted changes. |
| Race condition | A bug caused by unlucky timing between two operations. |
| Race window | The time gap where a race condition could happen. |
| Manual cleanup | A person or operations script fixes records the system intentionally did not auto-retry. |

%% ai-graph-start %%

**Related notes:**
- [[03 Cascade Attempt]]
- [[08 Glossary for Newbies]]
- [[01 Overview - Folder Rename Cascade]]
- [[08 Decision Log]]
- [[05 Retry Flow]]

**Relations:**
- Backend — *IS_PART_OF* — Application
- API — *ENABLES* — Software systems TO_TALK
- Endpoint — *IS_A* — API
- Folder ID — *IS_A* — Identifier
- Folder ID — *IDENTIFIES* — Folder
- Folder ID — *DOES_NOT_CHANGE_ON* — Folder name
- Folder name — *IS_A* — Label
- Folder name — *CAN_CHANGE* — true
- Cascade — *IS_A* — Chain_reaction
- Materialized data — *IS_A* — Data
- Materialized data — *MUST_BE* — Synchronized
- Tenant — *IS_A* — Customer_or_organization
- Tenant — *HAS* — Separated_data
- MongoDB — *IS_A* — Database
- MongoDB — *STORES* — Document
- Collection — *IS_A* — Group
- Collection — *CONTAINS* — Document
- Collection — *IS_SIMILAR_TO* — Table
- Table — *IS_IN* — SQL databases
- Document — *IS_A* — Record
- Document — *IS_STORED_IN* — MongoDB
- Field — *IS_A* — Value
- Field — *IS_INSIDE* — Document
- Array — *IS_A* — List_of_values
- Filter — *AFFECTS* — Document
- `updateMany` — *IS_A* — MongoDB_command
- `updateMany` — *UPDATES* — Document
- `updateMany` — *USES* — Filter
- Aggregation pipeline — *IS_A* — List_of_steps
- Aggregation pipeline — *COMPUTES* — `_folderNames`
- Index — *IS_A* — Structure
- Index — *HELPS* — Database FIND Records
- Service — *IS_A* — Component
- Service — *OWNS* — Business logic
- Repository — *IS_A* — Class
- Repository — *TALKS_TO* — Database
- Facade — *IS_A* — Front_door
- Facade — *HIDES* — Logic
- Event — *IS_A* — Message
- Observer — *LISTENS_FOR* — Event
- Observer — *REACTS_TO* — Event
- Async — *MEANS* — Work_happens_in_background
- Executor — *IS_A* — Component
- Executor — *RUNS* — Background work
- Exception — *IS_AN* — Error_object
- Exception — *IS_IN* — Java
- Exception — *INDICATES* — Normal flow could_not_continue
- Retry — *IS_A* — Process
- Retry — *APPLIES_TO* — Unfinished work
- Retry — *APPLIES_TO* — Failed work
- Marker — *IS_A* — Record
- Marker — *REMEMBERS* — Unfinished cascade work
- Lock — *PREVENTS* — Simultaneous work
- Partial — *MEANS* — Only_some_work_finished
- Timeout — *MEANS* — System_stopped_waiting
- Idempotent — *MEANS* — Safe_to_run_multiple_times
- Idempotent — *MEANS* — Does_not_cause_extra_unwanted_changes
- Race condition — *IS_A* — Bug
- Race condition — *IS_CAUSED_BY* — Unlucky_timing
- Race window — *IS_A* — Time_gap
- Race window — *IS_WHERE* — Race_condition_could_happen
- Manual cleanup — *FIXES* — Records
- Manual cleanup — *IS_PERFORMED_BY* — Person
- Manual cleanup — *IS_PERFORMED_BY* — Operations script

%% ai-graph-end %%