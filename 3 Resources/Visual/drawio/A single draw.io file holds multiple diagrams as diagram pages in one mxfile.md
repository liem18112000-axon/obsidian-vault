---
ai_hash: 7c2020f1a3b80dd5
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-23
entities: []
source: session 2026-07-23 leocdp-personalization-engine
status: seedling
tags:
- drawio
- diagrams
- xml
title: A single draw.io file holds multiple diagrams as diagram pages in one mxfile
type: concept
---

# A single draw.io file holds multiple diagrams as diagram pages in one mxfile

A single `.drawio` / `.drawio.xml` file can contain many diagrams: the root `<mxfile>` element holds one `<diagram id="..." name="...">` child per page, and each diagram carries its own `<mxGraphModel><root>` with the two mandatory bootstrap cells (`<mxCell id="0"/>` and `<mxCell id="1" parent="0"/>`).

Key points:

- Cell ids are **scoped per page** — every `<diagram>` restarts its own id namespace, so `id="title"` can exist on all pages without conflict.
- draw.io opens **plain uncompressed XML** fine; no base64/deflate encoding of the model is required (that encoding is only what the app produces by default when saving).
- Each `<mxGraphModel>` can set its own `pageWidth`/`pageHeight`, so pages can have different canvas sizes.

Useful when asked to merge several diagrams "into 1 file only" — one page per source diagram.

Minimal skeleton:

```xml
<mxfile host="app.diagrams.net">
  <diagram id="p1" name="Page 1">
    <mxGraphModel ...><root>
      <mxCell id="0"/><mxCell id="1" parent="0"/>
      <!-- vertices / edges parent="1" -->
    </root></mxGraphModel>
  </diagram>
  <diagram id="p2" name="Page 2">...</diagram>
</mxfile>
```

Validate well-formedness on Windows with `[xml]$x = Get-Content -Raw file.xml` (console may mangle UTF-8 arrows like `→` when printing — that is display-only, not file corruption).

## Related

- [[Convert Excalidraw to draw.io by reading exported PNGs instead of the JSON]]

%% ai-graph-start %%

**Related notes:**
- [[Convert Excalidraw to draw.io by reading exported PNGs instead of the JSON]]

%% ai-graph-end %%