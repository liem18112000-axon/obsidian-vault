---
title: "Deck one-pager from an Excalidraw section: rebuild as a 2x2 grid, not a crop"
created: 2026-08-26
type: howto
status: seedling
source: "session 2026-08-26"
tags: [excalidraw, diagramming, slides, layout, howto]
---

# Deck one-pager from an Excalidraw section: rebuild as a 2x2 grid, not a crop

To turn one section of a large Excalidraw diagram into a slide/deck **one-pager**, build a *standalone* `.excalidraw` scene for it rather than cropping the big PNG — the renderer auto-fits to the scene's content bounds, so a clean file crops tight with no manual pixel math.

**Layout gotcha:** if the source section is a wide horizontal band (title + a row of cards), extracting it verbatim renders as a ~5:1 strip — a banner, not a slide. Re-lay the same cards as a **2x2 grid** with the strap/subtitle wrapped to 2 lines; that yields ~16:10 (`3744x2392` at scale 2), which drops cleanly onto a 16:9 slide.

**Export both formats:** PNG (`-s 2`) for quick paste, **SVG** for decks — SVG scales without blur and stays tiny (~30KB vs ~500KB).

Keep the standalone builder as a small self-contained generator (emit rects+text with explicit newlines; Excalidraw free-text does not auto-wrap, so wrap lines yourself). See [[render_excalidraw.py output path needs -o flag, not positional arg]].

## Related

- [[render_excalidraw.py output path needs -o flag]]
- [[not positional arg]]
