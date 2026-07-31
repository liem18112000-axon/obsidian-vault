---
title: "Export a static .excalidraw from an Excalimate animated scene via get_scene"
created: 2026-06-16
type: howto
status: seedling
source: "session 2026-06-16 divide-conquer-count overview"
tags: [excalimate, excalidraw, animation, export, gotcha]
---

# Export a static .excalidraw from an Excalimate animated scene via get_scene

Calling `get_scene` on an Excalimate project returns each element with its **base** opacity (e.g. 100), even when reveal animations drive that element to opacity 0 at t=0. Opacity-0-at-start is stored in the animation tracks, not on the element. So you can dump the elements array and wrap it in an Excalidraw envelope to get a fully-visible **static** `.excalidraw` export of an animated scene:

```js
// node (no jq on this machine)
const els = JSON.parse(fs.readFileSync(getSceneDump, 'utf8'));
const out = { type:'excalidraw', version:2, source:'https://excalidraw.com',
  elements: els, appState:{viewBackgroundColor:'#ffffff', gridSize:null}, files:{} };
fs.writeFileSync('x.excalidraw', JSON.stringify(out, null, 2));
```

Note: get_scene output is large and gets spilled to a tool-results file — read that path, don't expect inline JSON. Use this when asked to 'export to a folder' an animated deck: ship both the share URL (animation) and a static .excalidraw (the still).

## Related

- [[Excalimate camera pan = translateX keyframes at each scene's center X]]
