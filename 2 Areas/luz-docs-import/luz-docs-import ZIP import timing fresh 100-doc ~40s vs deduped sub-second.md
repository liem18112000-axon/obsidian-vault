---
title: "luz-docs-import ZIP import timing: fresh 100-doc ~40s vs deduped sub-second"
created: 2026-08-11
type: observation
status: seedling
source: "session 2026-08-11 Gap-3 + dedup dev test"
tags: [luz-docs-import, performance, mongodb, zip-import]
---

# luz-docs-import ZIP import timing: fresh 100-doc ~40s vs deduped sub-second

Measured on dev (tenant d0783310…, 100-doc sample zips). The authoritative per-import duration is the import-job Mongo document's **`lastModifield − createdAt`**, which spans the whole cross-service pipeline the job drives (unzip → per-`*.metadata.json`-sidecar antivirus scan → jsonstore/vault → Mongo document + folder writes, under `FLUSH_EVERY_N=1000` / `FLUSH_EVERY_MS=10000` batching).

- **Fresh 100-doc import ≈ 40 s** (LEGACY 40.13 s, BASELINE 39.95 s) → ~0.4 s/doc, strikingly consistent.
- **Fully-deduped / skipped run = sub-second (0.35–0.72 s)** — no documents are created; the server only computes NFC dedup keys and matches existing docs, so `skipped=100 ok=0` returns almost instantly.

**Trust the Mongo delta, not the API poll for short jobs.** The test's WAY1 poll (`GET /import-jobs/{id}`) samples every 3 s with a floor, so it *overstates* tiny jobs — it reports "5 s" for a job that Mongo shows took 0.35 s. For fresh ~40 s imports the two agree; for skip runs only `lastModifield − createdAt` is accurate.

Context: dedup identity is `(importZipName, relativePath)` with `importZipName` = uploaded filename minus extension; re-uploading the same zip under the same name skips everything.

## Related

- [[ZIP entry names decode as CP437 mojibake when the UTF-8 EFS flag is unset]]
