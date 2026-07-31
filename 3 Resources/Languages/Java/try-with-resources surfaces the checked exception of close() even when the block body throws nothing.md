---
title: "try-with-resources surfaces the checked exception of close() even when the block body throws nothing"
created: 2026-06-05
type: lesson
status: budding
source: "DocMaterializeObserver refactor compile failure, session 2026-06-05"
tags: [java, exceptions, try-with-resources, gotcha]
---

# try-with-resources surfaces the checked exception of close() even when the block body throws nothing

In a try-with-resources block, the compiler counts the **implicit `close()` call** as a throw site. If the resource is a plain `AutoCloseable` (whose `close()` throws `Exception`), the enclosing method must catch or declare `Exception` — even when the block body itself throws nothing checked. The javac message is distinctive: *"unreported exception java.lang.Exception ... exception thrown from implicit call to close() on resource variable"*.

Two places this bites:
1. **Refactoring code out of a try/catch** — moving a try-with-resources from inside a catch-all method (e.g. an observer's try/catch) into a smaller hook method suddenly fails to compile, because the swallowing catch is gone. Fix: redeclare `throws Exception` on the hook (a template-method base that declares `handle() throws Exception` makes this legal), or catch locally.
2. **Designing closeable guards** — if a resource is a no-fail guard (like a thread-local suppression toggle), declaring its `close()` *without* `throws Exception` (or implementing `AutoCloseable` with a narrowed `@Override public void close()`) removes the burden for every caller.

Hit in luz_docs when extracting `DocMaterializeObserver.handle()` from the observer's try/catch: `ChangeTrackingSuppression.suppress()` returns an AutoCloseable whose close() declares Exception.

## Related

- [[CDI observer methods are inherited from superclasses but not across packages if package-private]]
