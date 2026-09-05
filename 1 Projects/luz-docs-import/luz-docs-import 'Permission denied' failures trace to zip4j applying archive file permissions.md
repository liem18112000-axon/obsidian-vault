---
title: "luz-docs-import 'Permission denied' failures trace to zip4j applying archive file permissions"
created: 2026-08-14
type: lesson
status: seedling
source: "session 2026-08-14 case 21 import test"
tags: [luz-docs-import, zip4j, import, bug, permissions]
---

# luz-docs-import 'Permission denied' failures trace to zip4j applying archive file permissions

When a `luz-docs-import` ZIP import reports a file as **failed** with detail `<extracted-path> (Permission denied)`, the cause is **not** the mount, the filename, or the name encoding — it is zip4j applying the archive entry's stored POSIX mode during `FileUtil.extractAllZipFile` (`zipFile.extractAll`). A Windows-made zip whose entry carries a high-word mode lacking owner-read (e.g. `----w----`) extracts a file the non-root `jboss` WildFly user cannot read → `FileInputStream` throws EACCES → `processDocumentFile` records it as failed. See [[zip4j extractAll applies a ZIP entry's stored Unix mode, so Windows-made zips can extract unreadable files]] for the underlying mechanism.

**Repro:** `data/Lam/import-test-zips/import-test-zips/21-edge-cp437-names.zip` (name is misleading — the entries are plain ASCII; the trigger is the external attrs). Single file `file_correct_name/Abschaltung von E-Post Office und.pdf` fails while the folder lists fine.

**Fix:** after `extractAll`, normalize permissions before `readAllFileInFolder`:
```java
zipFile.extractAll(folderPath);
Files.walkFileTree(Paths.get(folderPath), new SimpleFileVisitor<>() {
  public FileVisitResult preVisitDirectory(Path d, BasicFileAttributes a){ d.toFile().setReadable(true,true); d.toFile().setExecutable(true,true); return CONTINUE; }
  public FileVisitResult visitFile(Path f, BasicFileAttributes a){ f.toFile().setReadable(true,true); return CONTINUE; }
});
```
Set dir perms in `preVisitDirectory` (before descent) so the walk can enter an otherwise-`0o000` dir; the owner can always chmod its own files. Portable — no-ops on Windows local dev.

**Latent second bug uncovered:** `FileUtil.readAllFileInFolder` does `for (File e : folder.listFiles())` with no null-check. A zip that extracts a `0o000` **directory** makes `listFiles()` return `null` → NPE → whole job fails as `INTERNAL_SERVICE_ERROR`. The same permission-normalization fix prevents it; add a null guard too.

## Related

- [[zip4j extractAll applies a ZIP entry's stored Unix mode]]
- [[so Windows-made zips can extract unreadable files]]
