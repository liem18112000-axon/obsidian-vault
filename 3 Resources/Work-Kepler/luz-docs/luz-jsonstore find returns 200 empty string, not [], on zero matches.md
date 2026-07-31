---
title: "luz-jsonstore find returns 200 empty string, not [], on zero matches"
created: 2026-06-11
type: lesson
status: evergreen
source: "luz_jsonstore JsonStoreMongoDbResource.java, session 2026-06-11"
tags: [luz-jsonstore, luz-docs, api-contract, gotcha]
---

# luz-jsonstore find returns 200 empty string, not [], on zero matches

The luz-jsonstore find endpoint (`JsonStoreMongoDbResource.get`, POST /mdb/{tenant}/{collection}) returns **HTTP 200 with an empty-string body — not `[]` —** when the filter matches zero documents (`String ret = ""` + `// FIXME do it better` in source). This is a long-standing contract: clients MUST treat empty body as empty result.

luz_docs encodes the guard in `JsonObjectUtil.createArrayByString` (empty/null → empty JsonArray). Any code calling the raw REST client and parsing `res.readEntity(String.class)` directly with `Json.createReader(...).readArray()` breaks on zero matches with `JsonParsingException: EOF`.

Bitten by `MaterializeMigrationExecutor.getFolders`: documents with **orphaned folderIds** (folder deleted, doc still references it) made the folder find return the empty body → per-doc EOF failure every migration sweep. Earlier diagnosis as "intermittent jsonstore flake" was wrong — failures are deterministic per orphaned doc; the successful 631-byte responses were finds for other folder ids.

## Related

- [[JsonParsingException EOF offset -1 means an empty response body was parsed]]
- [[luz-jsonstore intermittently returns 200 with empty body on folder finds]]
