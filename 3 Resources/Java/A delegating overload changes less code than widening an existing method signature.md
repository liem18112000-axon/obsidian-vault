---
title: "A delegating overload changes less code than widening an existing method signature"
created: 2026-06-11
type: lesson
status: seedling
source: "luz_docs_statistic minimality audit, session 2026-06-11"
tags: [java, refactoring, code-review, minimal-diff]
---

# A delegating overload changes less code than widening an existing method signature

When an existing method needs an extra parameter (e.g. `MongoDBService.aggregate` needing a collection argument for the luz_docs_statistic folder count), adding the wider method and turning the old signature into a one-line delegate is usually the smallest possible diff: every existing caller, Mockito stub, and test invocation keeps compiling and matching unchanged. Widening the existing signature instead ripples into each call site and each strict-stub `Mockito.when(...)` whose argument matchers encode the old arity.

Rule of thumb when minimizing a review diff: count the *whole* diff including tests, not just the production file — an apparently redundant 4-line delegate often saves a dozen scattered test edits. Same logic applied to reusing `buildGroupStage(null, null)` for a count-only pipeline: tolerating a harmless extra `size:{$sum:1}` field on the wire was cheaper than maintaining a bespoke builder.

## Related

- [[Mockito strict stubbing turns removed production calls into UnnecessaryStubbingException test failures]]

> [!note] Counterpoint (user decision, 2026-06-11)
> In luz_docs_statistic the user explicitly rejected the delegating `aggregate` overload and asked for ONE explicit 4-arg method, adapting the 2 call sites and 2 test files instead. Rationale: a single method with an explicit `collection` argument is clearer than two near-identical entry points — API clarity beats raw diff size when the ripple is small. Use the overload trick only when the caller/test ripple is genuinely large.
