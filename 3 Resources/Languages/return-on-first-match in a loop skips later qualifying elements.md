---
ai_hash: 630064359cad890b
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-17
entities: []
source: session 2026-06-17
status: seedling
tags:
- gotcha
- control-flow
- code-review
title: return-on-first-match in a loop skips later qualifying elements
type: lesson
---

# return-on-first-match in a loop skips later qualifying elements

When a loop is meant to transform every element that meets a condition, but the condition is only **partly** visible at the loop level (the rest is checked inside a helper the loop calls), a \`return\` on the loop-level partial match silently skips later elements that would have fully qualified.

Example: loop returns on the first op with \`operator == REMOVE\`, but whether the op is actually rewritten depends on its \`path\` matching a target field — a check buried in the helper. The first REMOVE of an unrelated field triggers the \`return\` and the real target, later in the list, is never reached.

**Rule of thumb:** if the body uses an accumulator/builder and you want to touch *all* qualifying items, iterate-and-accumulate (reassign or append) rather than \`return\` on first hit. Only \`return\` early when you genuinely want exactly one match AND the loop-level condition is the *complete* match condition. Concrete instance: [[JSON-Patch remove-to-replace[] conversion skipped folderIds when another field's remove came first]].

## Related

- [[JSON-Patch remove-to-replace[] conversion skipped folderIds when another field's remove came first]]

%% ai-graph-start %%

**Related notes:**
- [[JSON-Patch remove-to-replace[] conversion skipped folderIds when another field's remove came first]]
- [[RFC-6902 replace at array index expects a scalar element not an array]]
- [[json-patch-independent-translation-breaks-reset-then-append]]
- [[flattenArrayAddOps runs only in materialize branch]]
- [[Materialize appendAsPatchOps uses RFC-6902 replace for sentinel fields]]

%% ai-graph-end %%