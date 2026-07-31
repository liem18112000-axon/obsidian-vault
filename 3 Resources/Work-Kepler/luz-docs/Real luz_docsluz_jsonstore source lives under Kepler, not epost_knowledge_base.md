---
title: "Real luz_docs/luz_jsonstore source lives under Kepler, not epost_knowledge_base"
created: 2026-07-14
type: reference
status: seedling
source: "session 2026-07-14 folderIds-facet investigation"
tags: [luz-docs, luz-jsonstore, repo, gotcha]
---

# Real luz_docs/luz_jsonstore source lives under Kepler, not epost_knowledge_base

The **actual git working copies** of the Luz services are under `C:\Users\dvtliem\Kepler\` — e.g. `C:\Users\dvtliem\Kepler\luz_docs` and `C:\Users\dvtliem\Kepler\luz_jsonstore`. These contain the real Java source you can grep and cite (`file:line`).

Gotcha: `C:\Users\dvtliem\AI\epost_knowledge_base\luz\luz_docs` (and its siblings) is only a **cached doc/index mirror with NO source code**. A search there for a handler or call site turns up docs/indexes, not the implementation. When investigating luz-docs/luz_jsonstore code, point tools at the `Kepler\` checkout, not `epost_knowledge_base`.

## Related

- [[luz-docs folderIds facet 500 is a Mongo $group memory-limit abort (error 292)]]
