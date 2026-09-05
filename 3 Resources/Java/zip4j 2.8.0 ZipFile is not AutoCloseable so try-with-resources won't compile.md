---
title: "zip4j 2.8.0 ZipFile is not AutoCloseable so try-with-resources won't compile"
created: 2026-08-11
type: lesson
status: seedling
source: "luz_docs_import LUZ-158230 · 2026-08-11"
tags: [java, zip4j, gotcha, maven]
---

# zip4j 2.8.0 ZipFile is not AutoCloseable so try-with-resources won't compile

In zip4j **2.8.0**, `net.lingala.zip4j.ZipFile` does **not** implement `Closeable`/`AutoCloseable`, so wrapping it in a try-with-resources (`try (ZipFile z = new ZipFile(path)) {…}`) fails to compile with *"cannot be converted to java.lang.AutoCloseable"*.

Use an explicit reference plus a `try/finally` if you need cleanup — or skip closing entirely (the extraction itself opens/closes per-entry streams; the `ZipFile` object mainly holds metadata). The important half of hardening extraction is not-swallowing the failure, not the close.

**Gotcha within the gotcha:** finding the jar in `.m2` or assuming a class implements an interface is not proof — a `mvn compile` is. Verify capabilities against the actual compile classpath, not by reading the dependency.

Related: [[Fail an import on a corrupt ZIP by translating the extraction exception to a domain FailureCode]]

## Related

- [[Fail an import on a corrupt ZIP by translating the extraction exception to a domain FailureCode]]
