---
title: "Audio-reactive anime mascot overlay for narrated videos (ffmpeg)"
created: 2026-06-19
type: howto
status: seedling
source: "session 2026-06-19"
tags: [ffmpeg, video, avatar, narration, presentation]
---

# Audio-reactive anime mascot overlay for narrated videos (ffmpeg)

To make a static anime mascot look like it is "presenting" a narrated slide video — without real lip-sync — composite two things in the bottom corner with ffmpeg:

1. **A rounded, bordered avatar card** built once in PIL: resize the portrait to card width, crop to head+shoulders, apply a rounded-rectangle alpha mask, and draw a few concentric rounded rectangles for a colored border ring. Save as RGBA PNG.
2. **An audio-reactive soundwave** generated from the narration track and a **gentle breathing bob** on both, via one filter_complex:

```
[0:a]showwaves=s=270x64:mode=cline:rate=30:colors=0x22D3EE,format=rgba,colorkey=0x000000:0.30:0.10[eq];
[0:v][1:v]overlay=W-w-44:H-h-40+8*sin(2*PI*t/3)[v1];
[v1][eq]overlay=W-270-44:H-40-64+8*sin(2*PI*t/3)[v]
```

Key tricks: `showwaves mode=cline` + `colorkey` on black makes the waveform a transparent overlay that visibly reacts to speech (reads as "talking"); the `+8*sin(2*PI*t/3)` term on the overlay Y gives a slow 3-second breathing bob; keep audio with `-c:a copy`. Tried true lip-sync via Hedra but the free tier blocks it, so the audio-reactive mascot is the practical substitute. Per-deck, parameterize by swapping the avatar PNG (e.g. avatar-base-2 vs base-1) to give each deck a different presenter while keeping the same code. Relates to [[Full-bleed slide images need ~16:9 aspect or their text renders too small]].

## Related

- [[Full-bleed slide images need ~16:9 aspect or their text renders too small]]
