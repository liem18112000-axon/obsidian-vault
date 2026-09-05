---
title: "luz-docs-import Gap-3: always-UTF-8 ZIP decode is deliberate; CP437 non-flagged zips are accepted best-effort"
created: 2026-08-13
type: lesson
status: seedling
source: "docs/zip-import-design-review.md + PR-B 2026-08-13"
tags: [luz-docs-import, zip, encoding, gap3, design-decision, LUZ-158230]
---

# luz-docs-import Gap-3: always-UTF-8 ZIP decode is deliberate; CP437 non-flagged zips are accepted best-effort

In **luz-docs-import** ZIP ingestion the charset handling is a DELIBERATE, documented decision — do not "fix" it as a bug.

`FileUtil.extractAllZipFile` forces `zipFile.setCharset(StandardCharsets.UTF_8)` unconditionally. Per `docs/zip-import-design-review.md` **Gap 3**, the chosen direction is: *always open with UTF-8 and accept that non-flagged legacy zips remain best-effort.* The doc table explicitly lists **"CP437 vs UTF-8 entry names (flag bit unset)" as ❌ NOT fixed / accepted limitation.**

**Why always-UTF-8:** it correctly decodes the common case for ePost — legacy zips whose bytes are UTF-8 but whose general-purpose bit 11 (EFS/UTF-8 flag) is unset (the `gap3-legacy.zip` case, checked by `gap3_test.sh`). A CP437 (true OEM code page, non-ASCII) zip is the opposite case and cannot be satisfied by the same static charset — the bytes are ambiguous without the flag.

**Consequence (gotcha):** test fixture `21-edge-cp437-names.zip` (CP437, EFS flag unset) mis-decodes its filename and the single doc fails downstream create. This is *within* the accepted best-effort limitation, NOT a regression. Flipping the charset to fix it would REGRESS the gap3-legacy case. The only way to satisfy both is a UTF-8-strict-then-CP437-fallback per-entry detector, which zip4j 2.8.0 does not expose raw name bytes for cleanly — so it is a product decision, not a quick fix.

## Related

- [[ePost ZIP-import metadata sidecar field types (senderCompanyId is numeric)]]
