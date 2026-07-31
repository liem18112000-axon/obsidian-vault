---
title: "MongoDB ObjectId timestamp identifies recently-inserted docs"
created: 2026-06-23
type: howto
status: seedling
source: "session 2026-06-23 eArchive seed"
tags: [mongodb, objectid, technique, earchive]
---

# MongoDB ObjectId timestamp identifies recently-inserted docs

A MongoDB `ObjectId` embeds its creation time in its **first 4 bytes (8 hex chars) = Unix seconds**. So `new Date(parseInt(oid.toString().slice(0,8),16)*1000)` recovers the insert time, and sorting `{_id: -1}` gives newest-inserted-first. This is the reliable way to isolate a recently-inserted batch of documents.

**Why it mattered:** in the eArchive synthetic seed data the `_createdDate` field is *faked* (a random date over the past 5 years), so it is useless for "what did I just insert". The `_id` timestamp is the only truthful insert clock. I used it to confirm a 2,993-doc top-up batch was cleanly separated from the prior run by a ~50s ObjectId-timestamp gap before considering a delete.

**Caveat:** ObjectId time resolution is 1 second and docs are generated in batches, so many docs share a timestamp and batches appear as clusters — look for the *gap between clusters*, not per-doc ordering, to find a batch boundary.

Relates to [[directConnection=true counts read only the connected node and can be stale on a secondary]].

## Related

- [[directConnection=true counts read only the connected node and can be stale on a secondary]]
