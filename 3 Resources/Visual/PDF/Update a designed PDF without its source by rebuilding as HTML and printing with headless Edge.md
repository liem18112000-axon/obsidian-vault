---
title: "Update a designed PDF without its source by rebuilding as HTML and printing with headless Edge"
created: 2026-07-20
type: howto
status: seedling
source: "session 2026-07-20"
tags: [pdf, pypdf, headless-chrome, edge, cv]
---

# Update a designed PDF without its source by rebuilding as HTML and printing with headless Edge

To "edit" a PDF whose source file is lost (e.g. a CV exported from Word), do a round-trip: extract the text (PDF reader), the images (`pypdf` `page.images`), and every hyperlink (`page["/Annots"]` → `/A` → `/URI`), rebuild the document as a styled HTML replica (photo inlined as a base64 data URI), and print it back to PDF with headless Edge/Chrome:

```
msedge --headless --disable-gpu --no-pdf-header-footer --print-to-pdf="out.pdf" "file:///.../cv.html"
```

Chromium preserves `<a href>` as real clickable PDF link annotations, so links survive. Verify the round-trip by diffing the sets of `/Annots` URIs extracted from the old and new PDFs — `orig_set - new_set` must be empty. Bonus: keep the HTML next to the PDF as the new editable source.

## Related

- [[Absolutely-positioned list bullets slide under a floated sidebar]]
