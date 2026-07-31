---
ai_hash: 33729a6d241b29a2
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- 07 Aggregation Pipeline
- Aggregation Pipeline
- MongoDB
- Stages
- JsonStoreQuerySearchUtil.buildDocumentQueryFilterWithSecurityClass()
- /search pipeline
- $sort
- $match
- $lookup
- $addFields
- $skip
- $limit
- $project
- _id index
- Top-k optimization
- /count pipeline
- $unwind
- $group
- 06 Server Filters
- 08 Facets
- search-logic
- _id
- folders
- security-class
- folderIds
- _folderNames
- pagination
- includes/excludes
- Index (database)
- restricted folders
---

# 07 — Aggregation Pipeline Order

## What's an aggregation pipeline?

MongoDB processes data in stages — see [[Glossary#Aggregation Pipeline|Aggregation Pipeline]]. Each stage's output is the next stage's input, like a factory conveyor belt. The **order** of stages matters a lot for performance and correctness.

The pipeline is built by `JsonStoreQuerySearchUtil.buildDocumentQueryFilterWithSecurityClass()`.

---

## `/search` pipeline

```
1. $sort       (ONLY when sorting by _id — uses the _id index for speed)
2. $match      (your client query + the four server filters from "06 Server Filters")
3. $sort       (non-_id fields — placed here for top-k optimization)
4. $lookup     (join folders to fetch folder name + securityClassCode)
5. $match      (security-class filter using the joined folder data)
6. $addFields  (drop restricted folders; append _folderNames if requested)
7. $skip       (pagination: skip `from` results)
8. $limit      (pagination: keep at most `size` results)
9. $project    (apply includes/excludes from the request body)
```

### Why this order? (plain-English walkthrough)

1. **`$sort` first if by `_id`** — `_id` always has an [[Glossary#Index (database)|index]], so sorting by it is free. Doing it early lets later stages stay sorted.
2. **`$match` next** — filter aggressively before doing any expensive work. Why join 1M folders if you only want 50 documents?
3. **`$sort` (other fields)** — placed after `$match` but **before** `$lookup`/`$skip`/`$limit` so MongoDB can do the [[Glossary#Top-k optimization|top-k trick]]: if you only need 10 results, stop sorting once you have 10.
4. **`$lookup`** — join with the folders collection to enrich each document with folder name + folder-level security class. Expensive! That's why it's late.
5. **`$match` (security on folder)** — now that we have the folder data, we can filter out documents whose folders are restricted.
6. **`$addFields`** — clean up the joined data, drop restricted folders, attach `_folderNames` if the caller asked.
7. **`$skip` + `$limit`** — paginate. Note: these are at the **end** so `totalRecordCount` reflects the full match set, not just one page.
8. **`$project`** — finally trim fields per `includes` / `excludes`.

---

## `/count` pipeline

`/count` has to be careful: a single document can live in multiple folders, and each folder may have its own security class. To count correctly, the pipeline **fans out** to one row per (document, folder) pair, filters, then **deduplicates** back.

Steps 1–6 are the same as `/search`. Steps 7+ differ:

```
7.  $unwind    (folderIds — fan out: one row per folder ID)
8.  $addFields ($toObjectId on each folderId so it can be joined)
9.  $lookup    (folder per unwound ID)
10. $addFields (merge inherited security-class codes from the parent folder chain)
11. $match    (security-class filter)
12. $group    (by _id — deduplicate and count)
```

### Why so different?

In `/search` we already removed restricted folders by step 6, and we don't need exact deduplication because we're returning a list. In `/count` we need the exact number of distinct documents the user is allowed to see, **including** documents whose security comes from an inherited parent folder. That's what `$unwind` + `$lookup` + `$group` accomplishes.

### Performance note

The `/count` pipeline is heavier than `/search`. If you don't need a count, pass `exclude-total-count=true` to `/search`.

---

**Navigation:** [[06 Server Filters|← prev]] · [[search-logic|↑ index]] · [[08 Facets|next →]]

%% ai-graph-start %%

**Related notes:**
- [[02 Endpoints]]
- [[search-logic]]
- [[Glossary]]
- [[Divide-and-Conquer Visible-Document Count]]
- [[Divide-and-Conquer Count - Technical Points for Beginners]]

**Relations:**
- MongoDB — *processes data using* — Aggregation Pipeline
- Aggregation Pipeline — *consists of* — Stages
- Aggregation Pipeline — *is built by* — JsonStoreQuerySearchUtil.buildDocumentQueryFilterWithSecurityClass()
- /search pipeline — *is a type of* — Aggregation Pipeline
- /count pipeline — *is a type of* — Aggregation Pipeline
- /search pipeline — *includes stage* — $sort
- /search pipeline — *includes stage* — $match
- /search pipeline — *includes stage* — $lookup
- /search pipeline — *includes stage* — $addFields
- /search pipeline — *includes stage* — $skip
- /search pipeline — *includes stage* — $limit
- /search pipeline — *includes stage* — $project
- $sort — *uses* — _id index
- _id — *has* — Index (database)
- $match — *uses* — 06 Server Filters
- $sort — *enables* — Top-k optimization
- $lookup — *joins* — folders
- $match — *filters by* — security-class
- $addFields — *drops* — restricted folders
- $addFields — *appends* — _folderNames
- $skip — *handles* — pagination
- $limit — *handles* — pagination
- $project — *applies* — includes/excludes
- /count pipeline — *shares stages with* — /search pipeline
- /count pipeline — *includes stage* — $unwind
- /count pipeline — *includes stage* — $group
- $unwind — *processes* — folderIds
- $group — *deduplicates* — documents
- $group — *counts* — documents
- /count pipeline — *differs from* — /search pipeline
- /count pipeline — *is heavier than* — /search pipeline
- 07 Aggregation Pipeline — *follows* — 06 Server Filters
- 07 Aggregation Pipeline — *is part of* — search-logic
- 07 Aggregation Pipeline — *precedes* — 08 Facets

%% ai-graph-end %%