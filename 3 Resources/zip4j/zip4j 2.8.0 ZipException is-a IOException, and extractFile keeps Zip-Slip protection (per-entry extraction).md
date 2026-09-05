---
title: "zip4j 2.8.0: ZipException is-a IOException, and extractFile keeps Zip-Slip protection (per-entry extraction)"
created: 2026-08-13
type: reference
status: seedling
source: "PR-B per-entry extraction 2026-08-13"
tags: [zip4j, java, zip-slip, luz-docs-import, gotcha]
---

# zip4j 2.8.0: ZipException is-a IOException, and extractFile keeps Zip-Slip protection (per-entry extraction)

zip4j 2.8.0 facts that shaped the luz-docs-import per-entry extraction (PR-B, F2):

1. **`net.lingala.zip4j.exception.ZipException extends java.io.IOException`** — so `catch (ZipException | IOException e)` is a COMPILE ERROR ("alternatives cannot be related by subclassing"). Just `catch (IOException e)` — it covers both the zip4j ZipException (bad entry, name too long) and plain IO errors.

2. **Zip-Slip protection is in the shared `AbstractExtractTask`**, so BOTH `extractAll()` and per-entry `extractFile(header, dest)` enforce the "illegal file name that breaks out of the target directory" check. Moving from `extractAll` to a per-entry loop does NOT lose slip protection. (I still added a defence-in-depth canonical-path guard.)

**Design pattern (F2):** replace all-or-nothing `extractAll` with a loop over `getFileHeaders()` + `extractFile` in a per-entry try/catch. One unextractable entry (name > 255-byte NAME_MAX, slip, IO) is recorded as a rejected entry and skipped; the rest of the archive still imports. A ZipException reading the central directory (truly corrupt archive) still propagates → whole job INVALID. This changes fixture 25 (path-traversal) from whole-archive INVALID to DONE-with-rejected-entries — safer/more granular, but a behavior change to flag.

## Related

- [[luz-docs-import Gap-3: always-UTF-8 ZIP decode is deliberate; CP437 non-flagged zips are accepted best-effort]]
