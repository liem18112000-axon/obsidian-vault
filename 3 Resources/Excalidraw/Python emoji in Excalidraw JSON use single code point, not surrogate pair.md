---
title: "Python emoji in Excalidraw JSON: use single code point, not surrogate pair"
created: 2026-08-20
type: lesson
status: seedling
source: "session 2026-08-20"
tags: [excalidraw, python, json, gotcha, diagrams]
---

# Python emoji in Excalidraw JSON: use single code point, not surrogate pair

When generating Excalidraw `.excalidraw` JSON from Python, do **not** write an emoji as a surrogate-pair escape like `"\ud83d\ude80"` in a string literal. Python treats those as two lone surrogates, and `json.dump(..., ensure_ascii=False)` then raises `UnicodeEncodeError: surrogates not allowed`. Use the **single code point** instead: `"\U0001F680"` (or paste the literal 🚀). This bit me while embedding a JSON sample inside a diagram.

Two related Excalidraw rendering rules (same session):
- **Standalone text does not auto-wrap** to its element `width` — long strings overflow the canvas/box. Pre-insert `\n` yourself and size the box to the wrapped line count.
- **Arrowheads render *inside* the target box** if the arrow's last point sits exactly on the box edge. Land the last point ~8px short of the edge (or set `endBinding.gap`) so the triangle shows in the gap.

## Related

- [[block/buzz architecture: a Nostr-relay hive mind for humans and AI agents]]
