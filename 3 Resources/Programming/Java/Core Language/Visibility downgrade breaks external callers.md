---
tags: [java, refactoring, gotcha]
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
