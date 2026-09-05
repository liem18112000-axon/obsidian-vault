---
title: "FilestoreUtils mount must pre-exist; SimpleTemporaryStorage defers the fail-fast check to first use"
created: 2026-08-11
type: lesson
status: seedling
source: "session 2026-08-11 luz_docs_import apply-file-store"
tags: [filestore, luz, java, gke, gotcha, testing]
---

# FilestoreUtils mount must pre-exist; SimpleTemporaryStorage defers the fail-fast check to first use

The Google Filestore mount directory (default `/uploads`, or `FILESTORE_MOUNT_LOCAL_PATH`) **must already exist** when `FilestoreUtils` (`ch.klara.luz:filestore_utils`) runs. Its constructor fail-fasts with `IllegalArgumentException` — `[FATAL] Mount path NOT FOUND: {path}` — if the root is absent, and unlike `commons-io` `FileUtils.copyInputStreamToFile` it only creates the `{date}/{uuid}` subfolders, **never the mount root itself**.

**Why it bites:** `SimpleTemporaryStorage` holds `FilestoreUtils` in a `static final` field, so the validation is **lazy** — it fires on the *first use* of the class as an `ExceptionInInitializerError`, **not at pod boot**. So a misconfigured mount gives no CrashLoopBackOff signal; the pod looks healthy and the first import blows up instead.

**Consequences to plan for:**
- Deployment: ensure the PVC/dir is mounted *and present* before the first request (a `RUN mkdir -p /uploads` in the Dockerfile for local/dev without a PVC).
- Unit tests: any test that touches `SimpleTemporaryStorage`/`FilestoreUtils` must set sysprop `filestore.mount.local.path` (or env `FILESTORE_MOUNT_LOCAL_PATH`) to an **existing** temp dir *before first class load* (JUnit `@TempDir` + a static/`@BeforeAll` initializer), or class init fails.

Discovered while planning the `luz_docs_import` zip-temp migration onto Filestore (branch `apply-file-store`).

## Related

- [[FilestoreUtils temp-path scheme and mount config resolution]]
