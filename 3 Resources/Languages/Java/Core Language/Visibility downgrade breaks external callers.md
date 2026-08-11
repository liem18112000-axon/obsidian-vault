---
ai_hash: 561ab429b6432749
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
entities: []
tags:
- java
- refactoring
- gotcha
---

# Visibility downgrade breaks external callers

When moving a method between classes during refactor, do **not** narrow its visibility back to `private` based only on inside-class usage. Other classes in the package may still call it.

## Gotcha

Sequence that bit me:
1. Method `andSecurityClassQuery` was originally `private` in `MaterializeQueryBuilder`.
2. Promoted to package-private (drop `private`) so an extracted sibling `MaterializeComputeBuilder` could call it.
3. Later reverted the extraction. Mistakenly re-added `private` because "only same class uses it now."
4. Build broke: `MaterializeFacade.countTotalDocumentByQuery` had ALSO started calling `andSecurityClassQuery` at some prior point. That external call was invisible from the QueryBuilder file alone.

## How to apply

Before flipping `package-private` → `private`, **grep the whole project** for the method name:

```
Grep pattern="\\.methodName\\(" type="java"
```

Only narrow if zero external call sites. IDE "find usages" works too — relying on local context inside one file is unsafe.

## Related

- [[CDI self-invocation bypasses interceptor proxy]] — another visibility/access-shape gotcha

%% ai-graph-start %%

**Related notes:**
- [[Mock at the facade boundary after consolidating logic behind a facade method]]
- [[Verify wildcard-to-explicit import cleanup by compiling]]
- [[A refactor that removes a method must grep tests for its name before merging]]
- [[Curate OpenRewrite UpgradeToJava25 - composite includes instance-main and wrapper bumps]]
- [[Materialize code review report - sprint-156 findings index]]

%% ai-graph-end %%