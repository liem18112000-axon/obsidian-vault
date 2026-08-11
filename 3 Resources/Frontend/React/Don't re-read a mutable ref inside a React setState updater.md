---
ai_hash: 0597118f698057cc
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-01
entities: []
source: session 2026-07-01 (Vinnstack ObsidianGraph)
status: seedling
tags:
- react
- hooks
- gotcha
- refs
- setState
title: Don't re-read a mutable ref inside a React setState updater
type: lesson
---

# Don't re-read a mutable ref inside a React setState updater

Inside a React `setState`/`useState` updater callback, do **not** read a mutable ref (e.g. `drag.current`). The updater can run asynchronously — after another event handler (like `mouseup`) has already reset the ref to `null` — so a non-null assertion such as `drag.current!.tx` throws `TypeError: Cannot read properties of null`.

**Fix:** snapshot the ref's fields into local `const`s *before* calling `setState`, and close over those locals in the updater.

```ts
const onMove = (e) => {
  const d = drag.current;
  if (!d) return;
  const { tx, ty } = d;               // snapshot BEFORE setView
  setView((v) => ({ ...v, tx: tx + dx, ty: ty + dy }));  // closes over locals, not the ref
};
```

Same reasoning applies to any value that can be mutated between scheduling and running the updater. The updater must be a pure function of its argument `v` plus captured immutable locals.

Real example: the pan/drag handler in Vinnstack `components/ObsidianGraph.tsx` — the drag handler and `onUp` (which nulls the ref) race.

%% ai-graph-start %%

**Related notes:**
- _(none above threshold)_

%% ai-graph-end %%