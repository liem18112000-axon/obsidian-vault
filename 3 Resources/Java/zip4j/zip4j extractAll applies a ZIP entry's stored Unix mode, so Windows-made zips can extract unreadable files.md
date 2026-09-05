---
title: "zip4j extractAll applies a ZIP entry's stored Unix mode, so Windows-made zips can extract unreadable files"
created: 2026-08-14
type: gotcha
status: seedling
source: "session 2026-08-14 luz-docs-import case 21"
tags: [zip4j, java, posix-permissions, gotcha, unzip]
---

# zip4j extractAll applies a ZIP entry's stored Unix mode, so Windows-made zips can extract unreadable files

zip4j's `ZipFile.extractAll()` restores each entry's **POSIX file mode from the ZIP external-file-attributes high word** when extracting on Linux/Mac. Its `FileUtils.applyPosixFileAttributes` only *skips* this when **both** `byte[2]` and `byte[3]` of the 4-byte external attr are zero — otherwise it reconstructs a mode and calls `Files.setPosixFilePermissions`.

The gotcha: a **DOS/Windows-created** zip (`create_system=0`) can store a non-zero high word that is *not* a valid readable mode. Example: file entry `external_attr=0x00100020` — low byte `0x20` is just the DOS "archive" flag, but the high word `0x0010` maps to POSIX `----w----` (`0o020`), i.e. **owner has no read**. On extraction the file becomes unreadable to a non-root process, so `new FileInputStream(f)` throws `FileNotFoundException: <path> (Permission denied)` (EACCES).

Info-ZIP/Unix zips avoid this (they store `0o100644`, owner-read set); zips with a fully-zero high word avoid it (skipped). The danger case is high-word non-zero *and* missing owner-read.

**Verified** in zip4j 2.8.0 (also present in 2.11.x); there is no `extractAll` flag to disable it. Safe fix: after `extractAll`, walk the tree and force owner read on files + read/execute on dirs.

Diagnosis signature: `File.listFiles()` sees the file (parent dir stayed readable), but opening it throws `Permission denied`.

Related: [[luz-docs-import Permission denied failures trace to zip4j applying archive file permissions]]

## Related

- [[luz-docs-import Permission denied failures trace to zip4j applying archive file permissions]]
