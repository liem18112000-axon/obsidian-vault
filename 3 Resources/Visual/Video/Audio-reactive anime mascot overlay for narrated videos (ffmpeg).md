---
title: "Audio-reactive anime mascot overlay for narrated videos (ffmpeg)"
created: 2026-06-18
updated: 2026-07-31
type: howto
status: seedling
source: "sessions 2026-06-18 / 2026-06-19"
tags: [ffmpeg, video, avatar, narration, presentation, vtuber, showwaves, vertex-ai, imagen, tts]
---

# Audio-reactive anime mascot overlay for narrated videos (ffmpeg)

Make a static anime mascot read as "presenting" a narrated video with **no lip-sync and no GPU** (Wav2Lip/SadTalker are impractical without one; Hedra/DomoAI are browser-only and paid). Composite two things into a bottom corner with one ffmpeg pass:

1. **A rounded, bordered avatar card**, built once in PIL: resize the portrait to card width, crop to head+shoulders, apply a `rounded_rectangle` alpha mask, draw a few concentric rounded rectangles as a colored border ring. Save RGBA PNG.
2. **An audio-reactive soundwave from the narration track**, plus a **breathing bob** on both:

```
[0:a]showwaves=s=270x64:mode=cline:rate=30:colors=0x22D3EE,format=rgba,colorkey=0x000000:0.30:0.10[eq];
[0:v][1:v]overlay=W-w-44:H-h-40+8*sin(2*PI*t/3)[v1];
[v1][eq]overlay=W-270-44:H-40-64+8*sin(2*PI*t/3)[v]
```

Why each piece: `showwaves mode=cline` + `colorkey` on black turns the waveform into a transparent overlay that visibly reacts to speech (this is what reads as "talking"); `+8*sin(2*PI*t/3)` on the overlay Y is a slow 3-second breathing bob; `-c:a copy` keeps the original narration so only video re-encodes. Parameterize per deck by swapping the avatar PNG (`avatar-base-1` vs `-2`) — same code, different presenter.

**Generating the base image (Vertex AI Imagen, REST).** Needs `aiplatform.googleapis.com` enabled. Token via `gcloud auth print-access-token` (fetch it in bash and pass to Python via env var — the `gcloud.cmd` shim isn't runnable from a Python subprocess on Windows). POST `https://us-central1-aiplatform.googleapis.com/v1/projects/<PROJ>/locations/us-central1/publishers/google/models/imagen-3.0-generate-002:predict` with header `x-goog-user-project: <PROJ>` and body `{"instances":[{"prompt":"..."}],"parameters":{"sampleCount":3,"aspectRatio":"3:4","personGeneration":"allow_adult"}}`. Images return as `predictions[i].bytesBase64Encoded`. `personGeneration: allow_adult` is what lets stylized anime characters through; prompt a plain/solid background so the corner cut-out is clean.

**Upgrade path to real lip-sync:** feed the base image + full narration mp3 to Hedra/DomoAI in the browser, download the clip, chromakey it into the same corner slot.

Context: `C:\Users\dvtliem\.claude\docs\hook-present\build\add-avatar.py` + `avatar/`.

## Related

- [[3 Resources/Visual/Presentations/Full-bleed slide images need ~169 aspect or their text renders too small]]
- [[3 Resources/Visual/Video/Hedra talking-avatar flow + gotcha video generation is paid-only (free tier blocks Generate)]]
- [[concept-to-video avatar overlay sits bottom-right — keep callouts clear]]
