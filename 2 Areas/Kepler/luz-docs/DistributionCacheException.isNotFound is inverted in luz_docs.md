---
ai_hash: 68c201cf1247a789
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-07
entities:
- DistributionCacheException.isNotFound()
- luz_docs
- src/main/java/ch/klara/luz/docs/cache/distribution/DistributionCacheException.java
- WebApplicationException
- Response.Status.NOT_FOUND
- MaterializeCache.get()
- sprint-156 materialize review
- CDI self-invocation bypasses interceptor proxy
source: materialize code review 2026-06-07
status: seedling
tags:
- luz-docs
- cache
- bug
- code-review
title: DistributionCacheException.isNotFound is inverted in luz_docs
type: gotcha
---

# DistributionCacheException.isNotFound is inverted in luz_docs

`DistributionCacheException.isNotFound()` in luz_docs (src/main/java/ch/klara/luz/docs/cache/distribution/DistributionCacheException.java) is logically inverted: it returns `true` when the wrapped cause status is **not** 404, and `false` for a genuine 404.

```java
return getCause() instanceof WebApplicationException w
    && w.getResponse().getStatus() != Response.Status.NOT_FOUND.getStatusCode();
```

Any consumer writing the natural idiom `if (!e.isNotFound()) throw e; return null;` gets the opposite of intent: genuine cache misses are rethrown, real 5xx errors are swallowed as a miss. Found live in MaterializeCache.get() during the sprint-156 materialize review. As of that review there is exactly one caller — either fix the method to `==` or invert at every call site. Prefer fixing the method.

## Related

- [[3 Resources/Languages/Java/CDI and MicroProfile/CDI self-invocation bypasses interceptor proxy]]

%% ai-graph-start %%

**Related notes:**
- [[luz-docs getDocumentById returns empty object not null for missing docs]]
- [[CDI self-invocation bypasses interceptor proxy]]
- [[DualCache L1 write ignores per-call TTL (uses domain default)]]
- [[A negative cache must be a distinct state from a cache miss, or its TTL is a dead write]]
- [[Gate behavior changes must update tests asserting old fallthrough in the same commit]]

**Relations:**
- DistributionCacheException.isNotFound() — *is in* — luz_docs
- DistributionCacheException.isNotFound() — *is located at* — src/main/java/ch/klara/luz/docs/cache/distribution/DistributionCacheException.java
- DistributionCacheException.isNotFound() — *is logically* — inverted
- DistributionCacheException.isNotFound() — *returns true when cause status is not* — 404
- DistributionCacheException.isNotFound() — *returns false for* — genuine 404
- DistributionCacheException.isNotFound() — *uses* — WebApplicationException
- DistributionCacheException.isNotFound() — *uses* — Response.Status.NOT_FOUND
- DistributionCacheException.isNotFound() — *found live in* — MaterializeCache.get()
- MaterializeCache.get() — *found during* — sprint-156 materialize review
- DistributionCacheException.isNotFound() — *is related to* — CDI self-invocation bypasses interceptor proxy

%% ai-graph-end %%