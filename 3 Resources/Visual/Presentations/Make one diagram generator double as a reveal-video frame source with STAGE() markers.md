---
title: "Make one diagram generator double as a reveal-video frame source with STAGE() markers"
created: 2026-06-19
type: howto
status: seedling
source: "session 2026-06-19"
tags: [excalidraw, pptx, video, diagram, slides]
---

# Make one diagram generator double as a reveal-video frame source with STAGE() markers

When a slide deck needs BOTH a static excalidraw diagram AND a staged-reveal video of that same diagram, don't maintain two copies of the drawing code. Make the generator build the elements once, drop `STAGE()` markers at each reveal boundary, and emit both outputs from the single `els` array:

```js
const bounds = []; const STAGE = () => bounds.push(els.length);
// ...draw title... STAGE();  ...draw card 1... STAGE();  ...etc
fs.writeFileSync(OUT, JSON.stringify({...full diagram, elements: els...}));   // the still
emit(els, FILES, bounds, framesDir, 'prefix', pinW, pinH);                     // cumulative frames
```

The shared `emit()` writes one `.excalidraw` per stage = `els.slice(0, bounds[k])`, each with TWO invisible white corner-pin rectangles at (0,0) and (pinW,pinH) so every partial frame renders at the SAME canvas size (otherwise excalidraw crops to content and frames jump). Then: render each frame to PNG, and feed the PNG sequence to a make-video script (Ken Burns + crossfade) → the reveal mp4. Embed the mp4 in pptx with `addMedia({type:'video', path, cover: <base64 png>, ...})` on a slide right BEFORE the still-diagram slide. This is the video+still pair pattern the obsidian/telegram decks use. Relates to [[3 Resources/Visual/Presentations/Full-bleed slide images need ~169 aspect or their text renders too small]].

## Related

- [[3 Resources/Visual/Presentations/Full-bleed slide images need ~169 aspect or their text renders too small]]
