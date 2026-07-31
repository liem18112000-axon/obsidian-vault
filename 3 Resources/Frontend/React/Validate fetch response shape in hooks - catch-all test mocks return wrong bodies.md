---
ai_hash: 7c23b1d955fe6a86
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
aliases:
- Guard array-typed React state seeded from a fetch with ?? []
created: 2026-07-03
entities: []
source: vinnstack usePrdComments crash 2026-07-03; vinnstack cloud-build fix 2026-07-26
status: seedling
tags:
- react
- hooks
- typescript
- testing
- fetch
- defensive-coding
- gotcha
title: Validate fetch response shape in hooks - catch-all test mocks return wrong
  bodies
type: lesson
---

# Validate fetch response shape in hooks - catch-all test mocks return wrong bodies

A hook that trusts its endpoint's shape (`if (j.ok) setState(j.items)`) can crash the ENTIRE host component tree on an **ok-but-wrong-shape** response: state becomes `undefined` and the next render throws at `state.filter(...)` / `state.length` / `state.slice(...)`. testing-library then shows an empty `<body>` with "Unhandled Errors" — a symptom far from the cause.

Where the bad body comes from: tests that stub fetch with a **catch-all URL matcher**. Embed a new child component with its own fetch into an existing tested component and the old mock happily answers the NEW endpoint with the OLD body (`{ok:true, interrogation}` instead of `{ok:true, comments}`), or a bare `{ok:true}` with the array field simply omitted. Partial-but-ok responses happen in prod too.

Guard at the assignment, two interchangeable forms:
- `if (j.ok && Array.isArray(j.comments)) setComments(j.comments)`
- `setVersions(j.exports ?? [])` for `useState<T[]>([])`

One cheap conjunct turns a whole-tree crash into a benign no-op, in prod and in tests alike. A cosmetic list should degrade to empty, never take the surface down.

Real cases (vinnstack, 2026-07): `usePrdComments`; and `MdExportControl` + `VersionChanges`, which both read `/api/export/md` this way and both crashed under a `{ok:true}` mock.

## Related

- [[A render crash masks latent crashes elsewhere in the same React subtree]]
- [[3 Resources/Frontend/React/Order-independent prefills fold precedence into the functional updater, not effect order]]

%% ai-graph-start %%

**Related notes:**
- [[A render crash masks latent crashes elsewhere in the same React subtree]]
- [[When a merge turns CI red decide test-vs-source fix by reading code intent]]
- [[Order-independent prefills fold precedence into the functional updater, not effect order]]
- [[Shape-keyed test mocks break when production query shapes change]]
- [[Inferred union returns grow optional-undefined members - annotate to keep in-narrowing]]

%% ai-graph-end %%