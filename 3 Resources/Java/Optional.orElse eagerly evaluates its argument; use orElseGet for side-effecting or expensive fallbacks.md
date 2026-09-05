---
title: "Optional.orElse eagerly evaluates its argument; use orElseGet for side-effecting or expensive fallbacks"
created: 2026-08-07
type: gotcha
status: seedling
source: "luz_docs_import folder-dup debug 2026-08-07"
tags: [java, optional, gotcha, idempotency, luz-docs-import]
---

# Optional.orElse eagerly evaluates its argument; use orElseGet for side-effecting or expensive fallbacks

`Optional.orElse(x)` evaluates `x` **eagerly and unconditionally** — the argument is computed before orElse runs, whether or not the Optional is present. `orElseGet(supplier)` is **lazy** — the supplier runs only when the Optional is empty. They look interchangeable but differ whenever the fallback is expensive or has side effects.

Gotcha that bit luz_docs_import folder dedup: 
```java
Optional.ofNullable(getFolderByPath(...))                      // may find an existing folder
    .orElse(viewControllerService.createFolder(...));          // BUG: create call fires every time
```
Because orElse's argument is always evaluated, `createFolder` was invoked for every folder even when an existing one was found. On a first import it looked fine (nothing existed, the created folder was the one used); on **re-import** getFolderByPath returned the existing folder AND createFolder still ran as a discarded side effect — creating duplicate folders server-side on every re-run. Files deduped correctly (separate skip-partition path); folders duplicated solely because of this. Fix: `orElseGet(() -> viewControllerService.createFolder(...))`.

Rule of thumb: if the fallback is a method call that does I/O, mutates state, or is costly, use `orElseGet`; reserve `orElse` for already-computed constants/values. The same eager-vs-lazy trap applies to `Objects.requireNonNullElse` vs `requireNonNullElseGet`, and Map `getOrDefault` vs `computeIfAbsent`.

Related: [[luz_docs_import]], [[luz_jsonstore find: project/sort/collation params must omit outer braces (server wraps them)]].

## Related

- [[luz_docs_import]]
