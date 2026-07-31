---
title: "Make an MP4 from staged Excalidraw reveal frames (corner-pin canvas + PIL blend + imageio-ffmpeg)"
created: 2026-06-18
type: howto
status: seedling
source: "session 2026-06-18"
tags: [excalidraw, video, mp4, imageio, ffmpeg, animation, pil]
---

# Make an MP4 from staged Excalidraw reveal frames (corner-pin canvas + PIL blend + imageio-ffmpeg)

To turn an Excalidraw diagram into a build-up **animation video** (no Excalimate export / no system ffmpeg needed):

## 1. Author cumulative reveal frames
Build the full diagram once in a generator, but tag reveal boundaries (a `STAGE()` that records `elements.length`). Emit one .excalidraw per stage = `elements.slice(0, boundary[k])`. Stage 0 = title only, last stage = full diagram.

## 2. Pin the canvas so every frame is the SAME size
The renderer auto-crops to the element bounding box, so partial frames would render at different sizes and not align. Add **two tiny invisible rects** (white fill+stroke, 2x2) at the extreme corners — e.g. (0,0) and (maxX,maxY) — to EVERY frame. Now all frames share one bbox → identical pixel dimensions. (Confirmed: 9 frames all rendered 8888x5288.)

## 3. Render each frame
`render_excalidraw.py frame.excalidraw --format png` per stage (the excalidraw-diagram skill renderer).

## 4. Assemble MP4 — bundled ffmpeg, no system install
`pip install "imageio[ffmpeg]"` ships a private ffmpeg (`imageio_ffmpeg.get_ffmpeg_exe()`), so no PATH/system ffmpeg required. Then:
```python
import numpy as np, imageio.v2 as imageio
from PIL import Image
w = imageio.get_writer('out.mp4', fps=30, codec='libx264', quality=8, macro_block_size=8)
# hold title; for each next stage: crossfade (Image.blend(prev,cur,a) for a in steps) then hold cur
w.append_data(np.asarray(pil_img))   # PIL -> numpy (NOT imageio.core.asarray — that attr does not exist on imageio.v2)
w.close()
```
Crossfading **cumulative** frames means only the newly-added elements fade in (shared pixels are identical) — a clean group-by-group reveal without per-element animation. Fit each frame onto a fixed 1920x1080 white canvas (contain+center) so libx264 gets even, constant dims.

Gotchas: `imageio.v2` has no `.core.asarray` → use `numpy.asarray(pil_image)`; libx264 needs even W/H (pad to 1920x1080); set `macro_block_size=8` to avoid resize warnings.

Result: `Hooks-and-Skills-intro.mp4` — 9 stages, ~21.6s, 1920x1080, 1.45 MB. Context: `C:\Users\dvtliem\.claude\docs\hook-present\build` (gen-harness-frames.js + make-video.py).

## Related

- [[Excalidraw text does not auto-wrap or auto-center]]
- [[Make one diagram generator double as a reveal-video frame source with STAGE() markers]]
