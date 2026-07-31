---
title: "Excalimate camera pan = translateX keyframes at each scene's center X"
created: 2026-06-16
type: howto
status: seedling
source: "session 2026-06-16 divide-conquer-count overview"
tags: [excalimate, excalidraw, animation, camera]
---

# Excalimate camera pan = translateX keyframes at each scene's center X

To pan the camera across a horizontal strip of scenes in Excalimate, lay scenes out at fixed X intervals (e.g. centers 800, 2800, 4800… for 1600-wide scenes with 400 gaps), then call `add_camera_keyframes_batch` with `property: "translateX"` whose **value is the target scene's center X in scene coordinates**. translateY is held constant (set once). `set_camera_frame({x,y,width,aspectRatio})` seeds the t=0 keyframe at scene 1's center and fixes zoom; later translateX keyframes move the view.

Pattern per scene: a hold pair (arrive-time → dwell-end, same value) then a move pair (dwell-end → next-arrive, next center). Use easeInOutCubic for the moves. Camera keyframe props are translateX/translateY/scaleX/scaleY — NOT the {x,y,width} shape shown in the skill doc's older examples.

Element reveals are timed to start just after the camera arrives (base opacity 0, two opacity keyframes 0→1, easeOutCubic; stagger 150ms within a scene).

## Related

- [[Export a static .excalidraw from an Excalimate animated scene via get_scene]]
