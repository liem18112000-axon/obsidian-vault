---
title: "FilestoreUtils temp-path scheme and mount config resolution"
created: 2026-08-11
type: concept
status: seedling
source: "session 2026-08-11 luz_docs_import apply-file-store"
tags: [filestore, luz, java, gke, nfs, reference]
---

# FilestoreUtils temp-path scheme and mount config resolution

`FilestoreUtils` (`ch.klara.luz:filestore_utils`, Java 17, JDK-only) writes each file to a generated, collision-free path:

```
{mount}/{yyyy_MM_ddTHH_mm}/{uuid}/{fileName}
```

- **Minute-resolution timestamp + random UUID** → human-browsable date grouping plus stateless uniqueness (no locking/coordination); safe for concurrent multi-pod writes.
- Sets POSIX **`GROUP_WRITE`** on created dirs/files so multiple containers sharing the Filestore PVC can read/write each other`s files (plain `0700` would block cross-container access).
- **Mount resolution order:** env `FILESTORE_MOUNT_LOCAL_PATH` -> sysprop `filestore.mount.local.path` -> MicroProfile Config (skipped gracefully if absent) -> default `/uploads`.
- API: `writeToFile(InputStream, fileName)` and `unzip(ZipInputStream, dest)`, both returning a `Path` and throwing `FileSystemException`.

`SimpleTemporaryStorage` (in `luz_store`) is a thin static wrapper: `writeFile(is, name)` -> `FS.writeToFile(...)` re-wrapping errors as `TemporaryStorageException`, plus `readFile(Path, opts)`. `DistributedTemporaryStorage` layers a `DualCache` `(token,tenantId,fileName)->absolutePath` on top for write-here / read-later-on-another-pod flows.

Note: `unzip` uses `java.util.zip.ZipInputStream` — it has **no** encryption/zip-bomb/explicit-UTF-8 handling, so services that need those (e.g. `luz_docs_import`, which uses zip4j) should keep their own extractor and use FilestoreUtils only for the *write*.

## Related

- [[FilestoreUtils mount must pre-exist; SimpleTemporaryStorage defers the fail-fast check to first use]]
