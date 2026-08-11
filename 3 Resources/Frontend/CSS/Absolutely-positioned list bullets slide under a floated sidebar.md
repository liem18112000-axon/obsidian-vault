---
ai_hash: 300f9ef66aeb3dc8
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-20
entities: []
source: session 2026-07-20
status: seedling
tags:
- css
- float
- print
- gotcha
title: Absolutely-positioned list bullets slide under a floated sidebar
type: lesson
---

# Absolutely-positioned list bullets slide under a floated sidebar

In CSS, a block box next to a float is NOT displaced — only its inline line boxes are. So a `li::before { position:absolute; left:0 }` bullet marker anchors to the li's block box edge, which extends underneath the floated sidebar, and the bullets render inside the sidebar area instead of beside their text.

Fix: make the marker inline and use a hanging indent instead of absolute positioning:

```css
li { padding-left: 3.6mm; text-indent: -3.6mm; }
li::before { content: "▪\00a0\00a0"; }
```

The inline `::before` flows in the (float-displaced) line box, so it always sits next to the text. Seen while rebuilding a two-column CV (floated sidebar + main flow) for print.

## Related

- [[Update a designed PDF without its source by rebuilding as HTML and printing with headless Edge]]

%% ai-graph-start %%

**Related notes:**
- [[PDF page count cannot prove a fixed-height page fits when overflow is hidden]]
- [[Update a designed PDF without its source by rebuilding as HTML and printing with headless Edge]]

%% ai-graph-end %%