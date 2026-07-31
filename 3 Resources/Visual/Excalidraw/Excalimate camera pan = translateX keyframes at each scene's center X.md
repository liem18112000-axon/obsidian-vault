---
ai_hash: 5f329a2544fe25ac
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-16
entities: []
source: session 2026-06-16 divide-conquer-count overview
status: seedling
tags:
- excalimate
- excalidraw
- animation
- camera
title: Excalimate camera pan = translateX keyframes at each scene's center X
type: howto
---

# Excalimate camera pan = translateX keyframes at each scene's center X

To pan the camera across a horizontal strip of scenes in Excalimate, lay scenes out at fixed X intervals (e.g. centers 800, 2800, 4800… for 1600-wide scenes with 400 gaps), then call `add_camera_keyframes_batch` with `property: "translateX"` whose **value is the target scene's center X in scene coordinates**. translateY is held constant (set once). `set_camera_frame({x,y,width,aspectRatio})` seeds the t=0 keyframe at scene 1's center and fixes zoom; later translateX keyframes move the view.

Pattern per scene: a hold pair (arrive-time → dwell-end, same value) then a move pair (dwell-end → next-arrive, next center). Use easeInOutCubic for the moves. Camera keyframe props are translateX/translateY/scaleX/scaleY — NOT the {x,y,width} shape shown in the skill doc's older examples.

Element reveals are timed to start just after the camera arrives (base opacity 0, two opacity keyframes 0→1, easeOutCubic; stagger 150ms within a scene).

## Related

- [[Export a static .excalidraw from an Excalimate animated scene via get_scene]]

%% ai-graph-start %%

**Related notes:**
- [[Export a static .excalidraw from an Excalimate animated scene via get_scene]]
- [[Make an MP4 from staged Excalidraw reveal frames (corner-pin canvas + PIL blend + imageio-ffmpeg)]]
- [[Excalimate cloud share links are CORS-broken — use Connect to MCP server instead]]
- [[Make one diagram generator double as a reveal-video frame source with STAGE() markers]]

%% ai-graph-end %%