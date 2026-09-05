---
title: "Pandoc Markdown to DOCX: full-width tables, justified body, per-section page breaks"
created: 2026-08-26
type: howto
status: seedling
source: "leo-customer360 release-doc DOCX work, session 2026-08-26"
tags: [pandoc, docx, markdown, documentation, ooxml, lua-filter]
---

# Pandoc Markdown to DOCX: full-width tables, justified body, per-section page breaks

When converting Markdown → MS Word (`.docx`) with pandoc, the three most-requested styling tweaks are NOT command-line flags — they need a **custom reference-doc** plus a **Lua filter**:

**1. Justified body text** — edit the reference-doc `word/styles.xml`: add `<w:jc w:val="both"/>` inside `<w:docDefaults><w:pPrDefault><w:pPr>`. It cascades to everything; single-line headings/table cells are visually unaffected (justify only changes lines that wrap).

**2. Page break before each section** — in the same styles.xml, add `<w:pageBreakBefore/>` to the `<w:pPr>` of the `Heading2` style (i.e. every `##`). Do NOT put it on `Heading1` if the doc title is an H1 — a page-break on the first paragraph creates a blank leading page.

**3. Full-width tables** — pandoc shrinks tables to content unless columns have explicit widths. A Lua filter that gives every column an equal width summing to 1.0 makes pandoc emit a percent-based full-width table (`<w:tblW w:type="pct">`):
```lua
function Table(t)
  local n=#t.colspecs; if n>0 then local w=1.0/n
    for i=1,n do t.colspecs[i]={t.colspecs[i][1],w} end end
  return t
end
```

**Build the reference-doc:** `pandoc -o ref.docx --print-default-data-file reference.docx`, patch its `word/styles.xml` (a docx is a zip; use Python `zipfile` + regex to insert the elements, then rewrite the zip). Convert with `pandoc in.md -o out.docx -f gfm --reference-doc=custom-reference.docx --lua-filter=fullwidth-tables.lua --toc --toc-depth=3`.

**Gotchas:** run pandoc from the folder containing the `.md` (or set `--resource-path`) so relative `./resources/*.png` images embed into the docx (`word/media/`). A `Pandoc(doc)` Lua function can strip a manual "Table of contents" heading + its block so it does not duplicate the native `--toc`. Verify results by inspecting the generated OOXML (`word/document.xml` for `tblW`, `word/styles.xml` for `jc`/`pageBreakBefore`) — no Word/LibreOffice needed. Legacy binary `.doc` still requires Word/LibreOffice; pandoc only writes `.docx`.
