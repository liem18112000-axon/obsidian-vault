---
ai_hash: 9886348851a2dc96
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-14
entities: []
source: session 2026-07-14 folderIds-facet investigation
status: seedling
tags:
- luz-docs
- luz-jsonstore
- repo
- gotcha
title: Real luz_docs/luz_jsonstore source lives under Kepler, not epost_knowledge_base
type: reference
---

# Real luz_docs/luz_jsonstore source lives under Kepler, not epost_knowledge_base

The **actual git working copies** of the Luz services are under `C:\Users\dvtliem\Kepler\` — e.g. `C:\Users\dvtliem\Kepler\luz_docs` and `C:\Users\dvtliem\Kepler\luz_jsonstore`. These contain the real Java source you can grep and cite (`file:line`).

Gotcha: `C:\Users\dvtliem\AI\epost_knowledge_base\luz\luz_docs` (and its siblings) is only a **cached doc/index mirror with NO source code**. A search there for a handler or call site turns up docs/indexes, not the implementation. When investigating luz-docs/luz_jsonstore code, point tools at the `Kepler\` checkout, not `epost_knowledge_base`.

## Related

- [[luz-docs folderIds facet 500 is a Mongo $group memory-limit abort (error 292)]]

%% ai-graph-start %%

**Related notes:**
- [[luz-docs folderIds facet 500 is a Mongo $group memory-limit abort (error 292)]]
- [[luz-docs facet $unwind branch keys off client-supplied typearray, not schema]]
- [[Canary tenant eArchive folder list trips Mongo code 292 sort-memory-limit]]
- [[luz-docs API request bodies are only observable as downstream luz-jsonstore queries]]
- [[jsonstore projections need quoted JSON keys and Mongo 16MB doc limit caps single-doc snapshots]]

%% ai-graph-end %%