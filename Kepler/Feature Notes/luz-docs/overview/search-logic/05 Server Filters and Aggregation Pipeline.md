---
type: technical-section
topic: luz-docs search logic
level: beginner
---

# 05 Server Filters and Aggregation Pipeline

Parent: [[Kepler/Feature Notes/luz-docs/overview/search-logic.md]]

Related:
- [[03 Query Operators]]
- [[08 Beginner Technical Terms]]

---

## Client query is not the whole query

The client sends a query, but the server adds extra filters before MongoDB runs it.

Why?

Because some rules must always be enforced by the backend, not trusted to the client.

Examples:

- hide deleted records by default;
- hide documents still being created;
- enforce security classes;
- handle personal document credential mapping.

---

## Automatic filters

| Filter | Condition | Can bypass? |
|---|---|---|
| Deletion status | `_deletionStatus = "false"` or field absent | yes, with `include-deleted-records=true` |
| Being-created guard | `is_being_created` absent or false | no |
| Security class | document/folder must match caller security codes | yes, with `skip-security-classes=true` |
| Credential mapping | personal documents filtered by credential token | no |

All client conditions and server filters are combined under a top-level `$and`.

Beginner meaning:

```text
client condition
AND not deleted
AND not being created
AND allowed by security
AND allowed by credential mapping
```

---

## What is an aggregation pipeline?

A MongoDB **aggregation pipeline** is a sequence of stages.

Each stage transforms or filters the data.

Mental model:

```text
documents enter stage 1
output goes to stage 2
output goes to stage 3
...
final result comes out
```

Factory analogy:

```text
raw material -> machine 1 -> machine 2 -> machine 3 -> finished product
```

---

## Search pipeline order

Built by:

```text
JsonStoreQuerySearchUtil.buildDocumentQueryFilterWithSecurityClass()
```

Pipeline:

```text
1. $sort
2. $match
3. $sort
4. $lookup
5. $match
6. $addFields
7. $skip
8. $limit
9. $project
```

---

## Stage-by-stage explanation

### 1. `$sort` for `_id`

Used only if sorting by `_id`.

Why before filtering?

> It can help MongoDB use an index before more complex pipeline stages happen.

### 2. `$match`

Filters documents.

This includes:

- client query;
- deletion filter;
- being-created filter;
- security-related filters.

### 3. `$sort` for non-`_id` fields

Sorts by fields other than `_id`.

The note says this is placed here for top-k optimization.

Beginner meaning:

> Sort before pagination so MongoDB can find the best first N results.

### 4. `$lookup`

Joins folder collection data.

Beginner meaning:

> Documents contain folder ids. `$lookup` loads the matching folder documents so the pipeline can inspect folder name and folder security.

### 5. `$match` on folder security

Filters based on folder security data loaded by `$lookup`.

### 6. `$addFields`

Adds or modifies fields.

Here it can:

- filter out restricted folders;
- append `_folderNames` if requested.

### 7. `$skip`

Skip records for pagination.

### 8. `$limit`

Limit how many records are returned.

### 9. `$project`

Choose which fields are returned.

This applies `includes` and `excludes`.

---

## Count pipeline difference

For `/count`, the pipeline does not need to return a page of documents.

Instead of `$skip`, `$limit`, `$project`, it uses stages like:

```text
7. $unwind
8. $addFields
9. $lookup
10. $addFields
11. $match
12. $group
```

Important stages:

| Stage | Meaning |
|---|---|
| `$unwind` | Split an array into one pipeline item per array element. |
| `$toObjectId` | Convert string id to ObjectId. |
| `$lookup` | Load folder data for each folder id. |
| `$group` | Group back by document `_id` to deduplicate and count. |

Why group by `_id`?

> A document may appear multiple times because it belongs to multiple folders or has multiple security-related matches. Grouping by `_id` prevents double counting.

