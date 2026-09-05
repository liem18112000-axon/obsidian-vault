---
title: "Fail an import on a corrupt ZIP by translating the extraction exception to a domain FailureCode"
created: 2026-08-11
type: lesson
status: seedling
source: "luz_docs_import LUZ-158230 · 2026-08-11"
tags: [luz-docs-import, error-handling, exceptions, java]
---

# Fail an import on a corrupt ZIP by translating the extraction exception to a domain FailureCode

A background job that extracts a ZIP and swallows the extraction exception can proceed to report **DONE** on a partial/empty import — silent data loss. Fix: stop swallowing it; let the low-level `ZipException` (a subtype of `IOException`) propagate, then **translate it in the service layer** into a domain exception carrying a meaningful failure code (e.g. `DocsImportBackgroundException(FailureCode.INVALID)`).

**Why translate rather than let it bubble raw:** the jobs handler has a two-tier catch — a specific `catch (DomainException)` that records `e.getFailureCode()`, and a generic `catch (Exception)` that records `INTERNAL_SERVICE_ERROR`. Translating at the call site means the client sees the precise, actionable code (**INVALID** = bad input ZIP) instead of a generic internal error. Keep the low-level util free of domain types; do the mapping one layer up.

Related: [[zip4j 2.8.0 ZipFile is not AutoCloseable so try-with-resources won't compile]]

## Related

- [[zip4j 2.8.0 ZipFile is not AutoCloseable so try-with-resources won't compile]]
