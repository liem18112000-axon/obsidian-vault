---
ai_hash: 2f55ae0f459bb35a
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-23
entities: []
source: session 2026-07-23
status: seedling
tags:
- ux
- selection
- popover
- react
- vinnstack
title: Merge competing selection popovers into one toolbar via a target registry
type: lesson
---

# Merge competing selection popovers into one toolbar via a target registry

Vinnstack had TWO popovers racing over every text selection: a global "Tiếng Việt | Explain" assist toolbar (fixed, z-999, mounted once in the layout) and PrdComments' local "Comment" pill (absolute, z-10, per document). Both positioned above the selection → the global one always covered the local one, making commenting invisible even after the discoverability fixes.

Fix pattern — **one toolbar, context-aware actions via a target registry**:
- A tiny module-level registry (`lib/shared/commentTargets.ts`): each commentable document registers `{ el, onComment }` on mount (cleanup on unmount; `el.isConnected` guard).
- The global toolbar, when reading the selection, checks `findCommentTarget(sel.anchorNode)` — if the selection sits inside a registered body, it renders "Comment" as the FIRST action.
- `onComment` reads the LIVE selection itself (the toolbar's `onMouseDown preventDefault` keeps it alive through the click), builds the quote-anchor, and opens the composer. The local pill is deleted entirely.

Why a registry beats React context here: the toolbar is mounted in the root layout, the commentable bodies deep in feature trees — a plain module Set crosses that boundary with zero providers and works with any number of simultaneous documents.

General rule: when two features respond to the same user gesture (text selection, right-click, hover), don't stack independent popovers — merge into ONE surface where optional actions register themselves. Ordering = priority (Comment first because it's the document-work action; translate/explain are generic).

Related: [[Gesture-only features need an always-visible teacher - empty state must not hide the affordance]] (same feature, previous fix layer).

## Related

- [[Gesture-only features need an always-visible teacher - empty state must not hide the affordance]]

%% ai-graph-start %%

**Related notes:**
- [[Gesture-only features need an always-visible teacher - empty state must not hide the affordance]]
- [[Event-bus overlay components silently no-op where the singleton is not mounted]]

%% ai-graph-end %%