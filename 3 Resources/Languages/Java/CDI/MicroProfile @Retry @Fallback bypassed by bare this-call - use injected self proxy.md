---
title: "MicroProfile @Retry @Fallback bypassed by bare this-call - use injected self proxy"
created: 2026-06-07
type: gotcha
status: seedling
source: "materialize code review 2026-06-07"
tags: [microprofile, cdi, fault-tolerance, luz-docs, code-review]
---

# MicroProfile @Retry @Fallback bypassed by bare this-call - use injected self proxy

MicroProfile Fault Tolerance `@Retry` / `@Fallback` are CDI interceptors: they only fire when the call goes through the CDI client proxy. A bare `this.method()` (or implicit `method()`) call inside the same bean bypasses the proxy, so retry and fallback silently never run.

luz_docs convention: inject a self-reference and route annotated calls through it —

```java
@Inject
private MaterializeFolderParentChangeService self; // proxy
...
self.cascadeWithRetry(...); // interceptor fires
```

Gotcha found in review (sprint-156 materialize): `onFolderParentChange` called `getSnapshot(...)` via `this` while the very next line correctly used `self.cascadeWithRetry(...)` — so getSnapshot's @Retry AND its @Fallback (error-mapping to FolderException 500) were both dead. When reviewing any bean with @Retry/@Fallback/@Async/@Transactional-style annotations, grep for intra-class calls to the annotated methods.

Same trap exists in Spring AOP (self-invocation bypasses @Transactional/@Cacheable/@Retryable).

## Related

- [[luz_docs materialize feature]]
