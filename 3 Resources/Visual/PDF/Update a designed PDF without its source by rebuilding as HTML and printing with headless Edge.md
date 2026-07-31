---
ai_hash: f30d964015010032
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-20
entities: []
source: session 2026-07-20
status: seedling
tags:
- pdf
- pypdf
- headless-chrome
- edge
- cv
title: Update a designed PDF without its source by rebuilding as HTML and printing
  with headless Edge
type: howto
---

# Update a designed PDF without its source by rebuilding as HTML and printing with headless Edge

To "edit" a PDF whose source file is lost (e.g. a CV exported from Word), do a round-trip: extract the text (PDF reader), the images (`pypdf` `page.images`), and every hyperlink (`page["/Annots"]` → `/A` → `/URI`), rebuild the document as a styled HTML replica (photo inlined as a base64 data URI), and print it back to PDF with headless Edge/Chrome:

```
msedge --headless --disable-gpu --no-pdf-header-footer --print-to-pdf="out.pdf" "file:///.../cv.html"
```

Chromium preserves `<a href>` as real clickable PDF link annotations, so links survive. Verify the round-trip by diffing the sets of `/Annots` URIs extracted from the old and new PDFs — `orig_set - new_set` must be empty. Bonus: keep the HTML next to the PDF as the new editable source.

## Related

- [[Absolutely-positioned list bullets slide under a floated sidebar]]

%% ai-graph-start %%

**Related notes:**
- [[PDF page count cannot prove a fixed-height page fits when overflow is hidden]]
- [[Absolutely-positioned list bullets slide under a floated sidebar]]

%% ai-graph-end %%