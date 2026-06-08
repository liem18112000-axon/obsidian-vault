---
title: "DistributionCacheException.isNotFound is inverted in luz_docs"
created: 2026-06-07
type: gotcha
status: seedling
source: "materialize code review 2026-06-07"
tags: [luz-docs, cache, bug, code-review]
---

# DistributionCacheException.isNotFound is inverted in luz_docs

`DistributionCacheException.isNotFound()` in luz_docs (src/main/java/ch/klara/luz/docs/cache/distribution/DistributionCacheException.java) is logically inverted: it returns `true` when the wrapped cause status is **not** 404, and `false` for a genuine 404.

```java
return getCause() instanceof WebApplicationException w
    && w.getResponse().getStatus() != Response.Status.NOT_FOUND.getStatusCode();
```

Any consumer writing the natural idiom `if (!e.isNotFound()) throw e; return null;` gets the opposite of intent: genuine cache misses are rethrown, real 5xx errors are swallowed as a miss. Found live in MaterializeCache.get() during the sprint-156 materialize review. As of that review there is exactly one caller — either fix the method to `==` or invert at every call site. Prefer fixing the method.

## Related

- [[MicroProfile @Retry @Fallback bypassed by bare this-call - use injected self proxy]]
