---
title: "Declare a variable before the try so the catch block can log it"
created: 2026-08-13
type: lesson
status: seedling
source: "session 2026-08-13"
tags: [java, logging, error-handling, diagnostics]
---

# Declare a variable before the try so the catch block can log it

To include a value like a filename in a `catch` block's log message, **declare the variable before the `try` and assign it as the first statement inside the `try`** — not deeper in the block. A variable declared inside `try` is out of scope in `catch`; and even if hoisted, it will be `null`/unset if the failure happens before its assignment.

**Order the assignments by what you want to log.** Resolve the cheap, context-identifying values first (the filename, the id, the key) so they are already populated when a later, riskier call throws.

**Example** (`luz_docs_import` `DocsImportService.saveReferenceFileToTemp`): to log *which* zip failed, `filename` was hoisted above the `try` and resolved from the multipart headers *before* the risky `getBody()` / `writeFile()` calls, so a failure in either still logs the filename:

```java
String filename = null;
try {
    filename = FileMultipartUtil.getFileName(inputPart);   // cheap, resolve first
    InputStream fileIS = inputPart.getBody(InputStream.class, null);  // risky
    Path zipPath = SimpleTemporaryStorage.writeFile(fileIS, filename); // risky
    ...
} catch (IOException | TemporaryStorageException e) {
    LOGGER.severe("... tenantId: " + tenantId + " filename: " + filename + " : " + ExceptionUtils.getStackTrace(e));
}
```

Trade-off: hoisting the header-parse out of the guarded region means an exception *there* is no longer caught by this block — acceptable when that call is effectively non-throwing (header parse) and the payoff is diagnosable logs.

## Related

- [[RESTEasy multipart repeated field name yields a List]]
- [[get(0) silently drops extras]]
