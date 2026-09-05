---
title: "ZIP entry names decode as CP437 mojibake when the UTF-8 EFS flag is unset"
created: 2026-08-11
type: lesson
status: seedling
source: "session 2026-08-11 luz-docs-import Gap-3 testing"
tags: [python, zipfile, unicode, gotcha, luz-docs-import]
---

# ZIP entry names decode as CP437 mojibake when the UTF-8 EFS flag is unset

A ZIP entry stores its filename in the local/central directory header, and general-purpose bit 11 (0x800, the EFS / "language encoding" flag) declares that name is UTF-8. When that flag is **unset**, Python's `zipfile` falls back to decoding the raw name bytes as **CP437** — so a legacy zip whose names are actually UTF-8 bytes (common on macOS / older tooling) comes back as mojibake, e.g. `Ti├¬m chß╗ºng/` instead of `Tiêm chủng/`.

Recover the true name by reversing the bad decode:

```python
name = zi.filename
if not (zi.flag_bits & 0x800):          # EFS/UTF-8 flag unset
    name = name.encode("cp437").decode("utf-8")
```

Use `ZipFile.infolist()` (not `namelist()`) to reach `flag_bits`. Guard the recovery in try/except — a genuinely CP437 name will not round-trip through UTF-8. This mirrors the **luz-docs-import server's always-UTF-8 policy** (Gap-3 fix): it decodes every entry as UTF-8 regardless of the flag. A test oracle that trusts `zipfile`'s default decode will falsely fail the legacy case while the server is actually correct.

Found while writing `verify_gap3.py` for the luz-docs-import Gap-3 test — the bug was in the verifier's expected-name oracle, not the server.

## Related

- [[Windows Python stdout is cp1252 and crashes on emoji; reconfigure to UTF-8]]
- [[luz-docs-import ZIP import timing: fresh 100-doc ~40s vs deduped sub-second]]
