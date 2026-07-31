---
title: "eArchive count skeleton CSS classes are the reliable counts-loaded signal"
created: 2026-07-21
type: lesson
status: seedling
source: "trace DOM snapshot 2026-07-21"
tags: [earchive, playwright, technique]
---

# eArchive count skeleton CSS classes are the reliable counts-loaded signal

eArchive count placeholders have dedicated CSS classes: `.folder-count-skeleton` (per-folder "K Files" badge) and `.section-title-count-skeleton` (the "Documents (N)" / "Custom (M)" heading counts). Found by grepping the Playwright trace.zip DOM snapshot resources for `skeleton` — no live DevTools session needed.

They make a deterministic "counts finished loading" signal for automation: a skeleton seen then gone means the count rendered; badge-less tiles (company root, Trash) never show one. Beats both exact badge-count matching (unreachable when some tiles never badge) and quiet-period settling (exits early — badges resolve up to ~40 s in while nothing else changes). Used in trace-earchive.js collectPageEnter done-check and reported as "all folder-badge skeletons resolved".

## Related

- [[eArchive company-root and Trash tiles never render K Files badges]]
