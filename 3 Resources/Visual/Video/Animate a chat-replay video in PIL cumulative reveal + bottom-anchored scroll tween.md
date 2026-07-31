---
title: "Animate a chat-replay video in PIL: cumulative reveal + bottom-anchored scroll tween"
created: 2026-06-19
type: howto
status: seedling
source: "session 2026-06-19"
tags: [pil, pillow, ffmpeg, animation, video, chat]
---

# Animate a chat-replay video in PIL: cumulative reveal + bottom-anchored scroll tween

To fake a 'screen recording' of a chat (Telegram, iMessage, etc.) without a real device, render it frame-by-frame in PIL and stitch with ffmpeg:

- **Cumulative reveal**: keep a list of message specs; for step k render the canvas with messages[0:k]. New messages 'arrive' as k grows.
- **Bottom-anchored scroll**: the chat canvas is tall; the phone viewport shows only the bottom SCROLL_H px (a real chat sticks to the newest message). `view(canvas, anchor)` crops `[anchor-SCROLL_H : anchor]`.
- **Smooth auto-scroll**: when a step adds height, tween the anchor from the previous canvas height to the new one over ~0.35s with smoothstep (`f*f*(3-2f)`) so new messages slide up instead of jumping.
- **Live spinner / elapsed**: for a 'working…' step, re-render the canvas each frame with the animated glyph + incrementing seconds.
- Composite the viewport into a static phone-bezel page (built once) and `ffmpeg -framerate 30 -i f%05d.png -pix_fmt yuv420p out.mp4`.

Font gotchas (Windows): the **braille spinner** glyphs ⠋⠙⠹… live in the Braille block (U+2800–28FF) and are NOT in Segoe UI — render that line with **Segoe UI Symbol (seguisym.ttf)**, which has them, or you get tofu. Color emoji still need the run-splitting trick in [[Render color emoji in PIL by splitting runs onto the Segoe UI Emoji font]] — and remember EVERY text draw that may contain emoji (including side captions) must use the emoji-aware path, not a bare draw.text. Built the telegram-present remote-control demo recording this way.

## Related

- [[Render color emoji in PIL by splitting runs onto the Segoe UI Emoji font]]
