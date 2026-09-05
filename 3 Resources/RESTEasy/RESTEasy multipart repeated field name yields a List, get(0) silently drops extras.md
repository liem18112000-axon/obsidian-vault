---
title: "RESTEasy multipart: repeated field name yields a List, get(0) silently drops extras"
created: 2026-08-13
type: gotcha
status: seedling
source: "session 2026-08-13"
tags: [resteasy, jax-rs, multipart, luz-docs, gotcha]
---

# RESTEasy multipart: repeated field name yields a List, get(0) silently drops extras

In RESTEasy, `MultipartFormDataInput.getFormDataMap().get(fieldName)` returns a `List<InputPart>`, not a single part. When a client repeats the same multipart field name (e.g. several parts all named `file`), every one of them lands in that list.

**The gotcha:** code that reads only `docs.get(0)` will silently drop the extra parts — no error, no warning. From the caller's side this is silent data loss (they sent 3 files, 1 was processed).

**Where it bit us:** `luz_docs_import` `DocsImportService.createTempFile` did exactly this — multiple uploaded zips meant only the first was imported and the rest were discarded (their RESTEasy-buffered temp files got cleaned up by `documents.close()` but were never read).

**Guard applied:** reject `docs.size() > 1` with HTTP 400 (`DocsImportException("Only one zip file can be imported per request", SC_BAD_REQUEST)`), so callers get a clear error instead of silent loss. Alternative, if multi-file is desired: loop over `docs` instead of taking `get(0)`.

**General lesson:** when consuming a multipart map, always decide explicitly what `size() != 1` means — reject, loop, or merge. Never let `.get(0)` be an implicit "take the first, ignore the rest".
