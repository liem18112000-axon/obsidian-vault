---
title: "LibreOffice headless convert-to leaves soffice.bin locking the source file — silent stale writes"
created: 2026-06-18
type: lesson
status: seedling
source: "session 2026-06-18"
tags: [libreoffice, soffice, pptx, file-lock, windows, gotcha, python-pptx]
---

# LibreOffice headless convert-to leaves soffice.bin locking the source file — silent stale writes

After `soffice --headless --convert-to pdf …` returns, a **soffice.bin process lingers** in the background (LibreOffice keeps a warm instance). That process keeps the **source file open/locked**. On Windows, a subsequent program that rewrites the SAME file (e.g. pptxgenjs `writeFile`, or any generator) can then **silently fail** — the tool reports success but the bytes on disk are NOT updated, leaving a STALE file. You then debug "wrong slide" symptoms against an old artifact.

Symptom seen: rebuilding a .pptx repeatedly printed "WROTE … 25 slides" while the file on disk stayed a stale 27-slide version with wrong/garbled slides. The diagrams (rendered PNGs) were fresh; only the assembled deck was stale.

## Fixes
- **Kill LibreOffice before any rebuild that overwrites a file LO has touched:**
  ```powershell
  Get-Process soffice,soffice.bin -EA SilentlyContinue | Stop-Process -Force
  ```
  (or `pkill soffice` on *nix). Then `rm` the target and regenerate.
- **Verify deck structure with python-pptx, NOT by re-rendering via LibreOffice** — soffice can also serve a stale/cached PDF, compounding the confusion. `Presentation(path)` reads the real file; hash each `shape.image.blob` (shape_type==13) against source PNGs to prove which image is on which slide.
- Prefer `rm target && regenerate` so a blocked write fails loudly (missing file) instead of silently leaving the old one.

Root family: same class as the EBUSY 'PowerPoint open → write silently fails' trap — any app holding the file open blocks the overwrite. After this fix the rebuilt deck verified at the expected 25 slides with every image hash-matched.

Context: Claude Hooks & Skills deck, `C:\Users\dvtliem\.claude\docs\hook-present`.

## Related

- [[3 Resources/AI/Claude-Code/pptx/QA a pptx on Windows LibreOffice to PDF then PyMuPDF render (thumbnail.py AF_UNIX fails)]]
