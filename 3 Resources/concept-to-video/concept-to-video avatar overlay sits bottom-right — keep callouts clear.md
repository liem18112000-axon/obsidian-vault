---
title: "concept-to-video avatar overlay sits bottom-right — keep callouts clear"
created: 2026-06-27
type: lesson
status: seedling
source: "session 2026-06-27 count-fanout deck"
tags: [concept-to-video, diagrams, avatar, gotcha]
---

# concept-to-video avatar overlay sits bottom-right — keep callouts clear

The concept-to-video skill's `add-avatar.py` overlays the audio-reactive mascot as a fixed card in the **bottom-right corner** of every slide (~270px wide × ~324 tall, from `avatar-base-*.png`; width set by `avatarWidth` in `deck.config.json`). Any diagram content — callouts, legends, captions — that lands in that lower-right region gets covered.

**Apply this when** designing a diagram whose still goes on a slide that will get the avatar overlay: keep the bottom-right ~270×324 zone empty, or shrink `avatarWidth`.

I hit this on an `engine-code` diagram: a right-hand column of five callouts ran to the slide's bottom edge, and the two lowest were clipped by the mascot. Fix was to merge them into four so the lowest callout ended above the avatar's top edge — no re-layout of the rest needed.

Note the avatar video is a *separate* artifact from the clean narrated video, so the un-overlaid `<deck>-<LANG>.mp4` is unaffected; only `<deck>-<LANG>-avatar.mp4` shows the clipping.

## Related

- [[concept-to-video reveal-clip truncation]]
