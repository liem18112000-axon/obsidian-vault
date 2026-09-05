---
title: "luz_jsonstore find: project/sort/collation params must omit outer braces (server wraps them)"
created: 2026-08-07
type: gotcha
status: seedling
source: "luz_docs_import dedup 500 debug 2026-08-07"
tags: [luz-jsonstore, mongodb, rest-client, gotcha, idempotency]
---

# luz_jsonstore find: project/sort/collation params must omit outer braces (server wraps them)

luz_jsonstore's find/getMany endpoint (POST mdb/{tenant}/{collection}) treats its `project`, `sort`, and `collation` **query params** as the INNER body of a JSON object and wraps them in braces itself:

```java
Document projectFields = Document.parse("{" + project + "}");   // getMany, JsonStoreMongoDbService
Document sortDoc      = Document.parse("{" + sort + "}");
Collation collation   = new Gson().fromJson("{" + collationString + "}", Collation.class);
```

So the caller must pass the field list WITHOUT surrounding braces, e.g. project=`"successfulFiles":1,"skippedFiles":1` — NOT `{"successfulFiles":1,...}`. If you include the braces, the server builds `{{...}}`, `Document.parse` throws, and getMany's catch-all returns **HTTP 500 with an empty body** (no message). 

Contrast: the **filter** is sent as the POST request body (a raw JsonObject) and used directly by `collection.find(filter)` — it DOES need to be a complete `{...}` object. Only the query-param fragments (project/sort/collation) are the brace-less ones. Easy to get inconsistent because body and query use opposite conventions.

How it bit us (luz_docs_import IdempotentImportService): PROJECTION was `{"successfulFiles":1,"skippedFiles":1}`; every find 500'd; the best-effort catch swallowed it and returned an empty already-imported set, so re-uploading the same zip re-imported every file (dedup silently disabled). Fix: drop the outer braces from PROJECTION.

General lesson: a **best-effort catch that returns a neutral/empty value** turns a hard failure (500) into a silent feature regression — log loudly (we did: 'could not load prior imported paths') and check that log when a feature 'does nothing'. The empty 500 body came from json-store's getMany catch-all, which also hides the real cause — see the luz_jsonstore missing-ExceptionMapper issue.

Related: [[luz_docs_import]], [[Read-side fire-and-forget mutation: pass the id and re-read in the async, don't mutate the object being serialized]].

## Related

- [[luz_docs_import]]
