---
ai_hash: eefa359be4e05586
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-18
entities: []
source: session 2026-06-18
status: seedling
tags:
- pptx
- libreoffice
- pymupdf
- windows
- gotcha
- claude-code
title: 'QA a pptx on Windows: LibreOffice to PDF then PyMuPDF render (thumbnail.py
  AF_UNIX fails)'
type: lesson
---

# QA a pptx on Windows: LibreOffice to PDF then PyMuPDF render (thumbnail.py AF_UNIX fails)

On Windows, the Anthropic **pptx** skill'\''s `scripts/thumbnail.py` (visual QA of a .pptx) fails with `module '\''socket'\'' has no attribute '\''AF_UNIX'\''` — it drives LibreOffice headless over a Unix domain socket, which Windows lacks. Use this two-step workaround instead.

## Workaround

1. **Convert the deck to PDF** with LibreOffice headless:
   ```bash
   "/c/Program Files/LibreOffice/program/soffice.exe" --headless --convert-to pdf --outdir . deck.pptx
   ```
   The stderr line `Could not find platform independent libraries <prefix>` is harmless; exit code is 0 and the PDF is written.

2. **Render PDF pages to PNG** with PyMuPDF (`fitz`) and view the PNGs:
   ```python
   import fitz
   d = fitz.open('deck.pdf')
   for i in (0, 4, 9):              # 0-based page indices
       d[i].get_pixmap(matrix=fitz.Matrix(1.3, 1.3)).save(f'slide-{i+1:02d}.png')
   ```

## Gotchas
- `pdftoppm`/poppler is NOT available, so the **Read tool cannot open the PDF directly** (`pdftoppm failed ... unsafe location`). PyMuPDF is self-contained and already installed — use it.
- The `MuPDF error: No common ancestor in structure tree` warnings during `get_pixmap` are **non-fatal**; the PNGs still write.

Discovered while building image-driven slide decks under `C:\Users\dvtliem\.claude\docs\hook-present` (the Claude Hooks & Skills talk).

%% ai-graph-start %%

**Related notes:**
- [[LibreOffice headless convert-to leaves soffice.bin locking the source file — silent stale writes]]
- [[pptxgenjs addImage stretches when wh aspect drifts from the real image — read PNG IHDR size]]

%% ai-graph-end %%