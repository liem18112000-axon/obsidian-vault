---
ai_hash: 3d000c3da6002a43
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-27
entities: []
source: session 2026-06-27 count-fanout deck
status: seedling
tags:
- concept-to-video
- diagrams
- avatar
- gotcha
title: concept-to-video avatar overlay sits bottom-right — keep callouts clear
type: lesson
---

# concept-to-video avatar overlay sits bottom-right — keep callouts clear

The concept-to-video skill's `add-avatar.py` overlays the audio-reactive mascot as a fixed card in the **bottom-right corner** of every slide (~270px wide × ~324 tall, from `avatar-base-*.png`; width set by `avatarWidth` in `deck.config.json`). Any diagram content — callouts, legends, captions — that lands in that lower-right region gets covered.

**Apply this when** designing a diagram whose still goes on a slide that will get the avatar overlay: keep the bottom-right ~270×324 zone empty, or shrink `avatarWidth`.

I hit this on an `engine-code` diagram: a right-hand column of five callouts ran to the slide's bottom edge, and the two lowest were clipped by the mascot. Fix was to merge them into four so the lowest callout ended above the avatar's top edge — no re-layout of the rest needed.

Note the avatar video is a *separate* artifact from the clean narrated video, so the un-overlaid `<deck>-<LANG>.mp4` is unaffected; only `<deck>-<LANG>-avatar.mp4` shows the clipping.

## Related

- [[concept-to-video reveal-clip truncation]]

%% ai-graph-start %%

**Related notes:**
- [[Audio-reactive anime mascot overlay for narrated videos (ffmpeg)]]
- [[concept-to-video skill turns a concept into deck, voiceover and narrated avatar video]]
- [[HTML-rendered chat demo videos serve over http, cumulative screenshots, pre-pad dark]]
- [[Make one diagram generator double as a reveal-video frame source with STAGE() markers]]

%% ai-graph-end %%