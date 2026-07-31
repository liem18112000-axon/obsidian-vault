---
title: "Cute animated presenter on a video: Vertex Imagen base + ffmpeg audio-reactive mascot (no GPU)"
created: 2026-06-18
type: howto
status: seedling
source: "session 2026-06-18"
tags: [vertex-ai, imagen, ffmpeg, avatar, vtuber, showwaves, video, tts]
---

# Cute animated presenter on a video: Vertex Imagen base + ffmpeg audio-reactive mascot (no GPU)

Two reusable tricks for adding a cute animated presenter to a video, no GPU needed.

## 1) Generate the anime base image with Vertex AI Imagen (REST)
Project had `aiplatform.googleapis.com` enabled. Token via `gcloud auth print-access-token` (fetch in bash, pass to Python by env var — the `gcloud.cmd` shim isn't runnable from Python subprocess on Windows). POST to:
`https://us-central1-aiplatform.googleapis.com/v1/projects/<PROJ>/locations/us-central1/publishers/google/models/imagen-3.0-generate-002:predict`
header `x-goog-user-project: <PROJ>`; body `{"instances":[{"prompt":"..."}],"parameters":{"sampleCount":3,"aspectRatio":"3:4","personGeneration":"allow_adult"}}`. Images come back as `predictions[i].bytesBase64Encoded` → base64-decode to PNG. `imagen-3.0-generate-002` worked; `personGeneration:allow_adult` lets stylized anime characters through. For a clean corner cut-out later, prompt a plain/solid background.

## 2) Audio-reactive 'talking mascot' overlay with pure ffmpeg (no lip-sync, no GPU)
No GPU here (Wav2Lip/SadTalker impractical) and Hedra/DomoAI are web-only — so true lip-sync needs a one-time browser step. A fully-automated *stylized* presenter instead:
- Build a rounded, bordered avatar **card** with PIL (rounded_rectangle alpha mask + a few outline passes for a colored border) → RGBA PNG.
- Composite over the video: a gentle **breathing bob** via a time expression in overlay (`overlay=W-w-44:H-h-40+8*sin(2*PI*t/3)`), plus an **audio-reactive soundwave** from the narration: `[0:a]showwaves=s=270x64:mode=cline:rate=30:colors=0x22D3EE,format=rgba,colorkey=0x000000:0.30:0.10` then overlay it under/over the card. `colorkey` on black makes the wave bg transparent so only the colored line shows.
- `-c:a copy` keeps the original narration; only the video re-encodes. Reads clearly as 'she's talking' and is on-theme (a HUD bar on her techwear) without faking lips.

Upgrade-to-lip-sync path: feed the base image + the full narration mp3 to Hedra/DomoAI in the browser, download the talking clip, then composite it into the same corner slot (chromakey if it has a solid bg).

Context: `C:\Users\dvtliem\.claude\docs\hook-present\build\add-avatar.py` + `avatar/`.
