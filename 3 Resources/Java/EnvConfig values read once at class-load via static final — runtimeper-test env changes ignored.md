---
title: "EnvConfig values read once at class-load via static final — runtime/per-test env changes ignored"
created: 2026-08-13
type: gotcha
status: seedling
source: "session 2026-08-13"
tags: [java, configuration, testing, classloading, gotcha]
---

# EnvConfig values read once at class-load via static final — runtime/per-test env changes ignored

In `luz_docs_import`, `EnvConfig.getInt/getLong` reads `System.getenv(name)` and clamps to `[min,max]` (blank/invalid -> default, logged). The gotcha is **where the result is stored**: the tuning knobs are assigned to `private static final` fields evaluated **once at class-load time**:

- `ImportDocumentExecutor.IMPORT_CONCURRENCY`
- `JobProgressWriter.FLUSH_EVERY_N` / `FLUSH_EVERY_MS`

**Implications:**
- Changing the env var at **runtime** has no effect — the value is frozen at first class-load.
- **Tests must set the env var BEFORE the class is loaded/initialised.** Setting it in `@BeforeEach` (after the JVM already loaded the class) is too late and silently uses the old/default value. Use a fresh classloader per case, a system-stub that forces re-init, or launch the JVM with the env already set.

**General lesson:** `static final X = readEnv(...)` couples configuration to classload order and makes per-test override hard. If runtime/tunable-per-test behaviour is wanted, read the env inside the method or via an injected config bean instead of a static-final field.

## Related

- [[Declare a variable before the try so the catch block can log it]]
