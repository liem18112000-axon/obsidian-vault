---
ai_hash: f423b0780dc88f28
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
aliases:
- Canvas edge overlap
- Canvas pierces node
created: 2026-06-11
entities: []
source: session 2026-06-11
status: seedling
tags:
- obsidian
- canvas
- json-canvas
- layout
- gotcha
title: Avoiding edge-through-node overlaps in Obsidian Canvas
type: lesson
---

# Avoiding edge-through-node overlaps in Obsidian Canvas

**Obsidian Canvas draws an edge as a near-straight line between the two endpoints' chosen sides — it does NOT auto-route around other nodes. So if you connect one parent to several nodes stacked in the same column, every edge to a lower node draws straight through the node(s) above it, and the line overlaps their text.** This is the most common "canvas looks messy / text overlaps" cause.

## The two failure patterns

1. **Fan to a vertical stack** — parent → child1, parent → child2, parent → child3 where the children share an x-column. The child2/child3 edges pierce child1/child2.
2. **Full-width loop / top-to-top edge** — an edge from the right-most node back to the left-most node arcs across the entire top band; its **label** lands wherever Obsidian puts the midpoint and collides with title/header nodes there.

## Fixes

- **Chain the stack vertically**: parent → child1 → child2 → child3 (each edge between *adjacent* nodes only). Short hops, nothing in between to pierce. Use `toEnd: "none"` on the intra-stack hops so they read as "belongs to" rather than flow.
- **Replace the long loop-back edge with a caption text node** placed in known-empty space (e.g. a top-row "↻ the loop repeats" note). A width-spanning edge's label position can't be controlled, so don't fight it.
- **Keep ≥28px between a group's top and its first child** so the group label doesn't sit on a node.
- **Verify programmatically**: parse the `.canvas` JSON and (a) check no two non-group node rectangles intersect, (b) sample points along each edge's endpoint-to-endpoint segment and flag any that fall inside a third node. Catches pierces the eye misses.

## Related

- [[Centering an embedded image in Obsidian]]
- [[Accesstrade API Integration - MOC]]

%% ai-graph-start %%

**Related notes:**
- [[Centering an embedded image in Obsidian]]
- [[Excalidraw text does not auto-wrap or auto-center]]

%% ai-graph-end %%