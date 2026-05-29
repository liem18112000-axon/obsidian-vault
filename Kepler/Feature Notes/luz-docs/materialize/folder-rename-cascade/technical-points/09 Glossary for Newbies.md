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

