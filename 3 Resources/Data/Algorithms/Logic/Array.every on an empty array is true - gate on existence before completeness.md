---
ai_hash: 5551047d4a22b423
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-03
entities: []
source: session 2026-07-03, vinnstack PRD tab gating bug
status: seedling
tags:
- javascript
- vacuous-truth
- gating
- vinnstack
title: Array.every on an empty array is true - gate on existence before completeness
type: gotcha
---

# Array.every on an empty array is true - gate on existence before completeness

`[].every(pred)` returns `true` (vacuous truth), so a readiness gate written as `items.every(isDone)` silently PASSES when the items were never created at all. In vinnstack this made the PRD tab appear right after the Business interrogation cleared - the technical track had zero questions, and `tech.every(isFinal)` on the empty array said "all committed".

Rule: a "stage X is complete" gate over a collection must check existence AND completeness: `xs.length > 0 && xs.every(isDone)`. Watch for the same trap in SQL (`NOT EXISTS (SELECT .. WHERE NOT done)` is vacuously true for zero rows) and in any all-quantifier over a possibly-empty set.

Corollary: when a UI gate mirrors a server-side readiness rule (here isPrdReady), copy the server's exact predicate - the server already had the length check; the UI reimplementation dropped it and the two drifted.

%% ai-graph-start %%

**Related notes:**
- [[Separate read-check from create in a first-run onboarding gate]]
- [[Idempotency guards keyed on object presence break when hydration materializes the object]]
- [[Validate fetch response shape in hooks - catch-all test mocks return wrong bodies]]

%% ai-graph-end %%