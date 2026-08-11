---
ai_hash: c179a3595a038dc7
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-14
entities: []
source: session 2026-07-14 folderIds-facet investigation
status: seedling
tags:
- luz-docs
- mongodb
- facet
- gotcha
title: luz-docs facet $unwind branch keys off client-supplied type:array, not schema
type: gotcha
---

# luz-docs facet $unwind branch keys off client-supplied type:array, not schema

In `luz-docs` facet building (`JsonStoreMongoService.getFacets`), whether the pipeline inserts `$unwind folderIds` before `$group` is decided **only by the client-supplied request field** `"type":"array"` — via `JsonObjectUtil.isFieldTypeArray()` comparing against `Constants.DOCUMENT_SEARCH_FACETS_ARRAY = "array"`. It is **not** derived from the actual document schema.

The gotcha: if a caller (e.g. a perf-test payload) omits or misdeclares `"type":"array"` for an array field like `folderIds`, the code takes the **no-unwind branch** and `$group._id` becomes the *raw, order-sensitive array per document*. With random per-document folder subsets that yields nearly one group per document (~800k), blowing the `$group` 100MB memory ceiling almost instantly — a much sharper trigger for [[luz-docs folderIds facet 500 is a Mongo $group memory-limit abort (error 292)]] than plain fan-out over the ~10 real folders would be.

> [!success] Confirmed by the 800k perf-test payloads (2026-07-14)
> The **failing** folderIds facet body is `{"terms":{"field":"folderIds","from":0,"size":2147483647}}` — **no `"type":"array"`** → no-unwind → dies with error 292.
> The **working** tags facet body is `{"terms":{"field":"tags","type":"array","sort":{"key":"asc"}}}` — **has `"type":"array"`** → unwind → succeeds (slow ~45s, but HTTP 200).
> Same code, opposite outcome, decided by one field. This resolves the earlier "unconfirmed" open item.

Lesson: caller-declared field type is load-bearing and untrusted here. A robust fix is to derive array-ness from the schema instead of trusting the request. When debugging, first check the exact facet payload the client sends.

## Related

- [[luz-docs folderIds facet 500 is a Mongo $group memory-limit abort (error 292)]]

%% ai-graph-start %%

**Related notes:**
- [[luz-docs folderIds facet 500 is a Mongo $group memory-limit abort (error 292)]]
- [[Mongo $group is blocking so time-to-error is scan-bound, not timeout-bound]]
- [[Canary tenant eArchive folder list trips Mongo code 292 sort-memory-limit]]
- [[Real luz_docsluz_jsonstore source lives under Kepler, not epost_knowledge_base]]
- [[luz-docs documentscount is ~130s on an 800k tenant — the 16-shard fan-out, not counting, is the bottleneck]]

%% ai-graph-end %%