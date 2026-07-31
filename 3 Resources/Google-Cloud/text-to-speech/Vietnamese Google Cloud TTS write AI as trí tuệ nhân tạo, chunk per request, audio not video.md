---
title: "Vietnamese Google Cloud TTS: write AI as trí tuệ nhân tạo, chunk per request, audio not video"
created: 2026-06-18
type: lesson
status: seedling
source: "session 2026-06-18"
tags: [google-cloud, tts, text-to-speech, vietnamese, ssml, gotcha]
---

# Vietnamese Google Cloud TTS: write AI as trí tuệ nhân tạo, chunk per request, audio not video

Notes for narrating a Vietnamese script with **Google Cloud Text-to-Speech** (`vi-VN`).

## The big gotcha: "AI" is read as the Vietnamese word "ai"
A `vi-VN` voice reads the two letters **AI** as the Vietnamese word *ai* (= "who"), and **"A.I."** (with dots) as *a chấm i chấm*. So in any Vietnamese TTS script, replace AI / A.I. with **"trí tuệ nhân tạo"** (the field) or **"trợ lý"** (the assistant). Same care for other dotted acronyms.

## Capabilities / limits
- Vietnamese voices exist: **Standard, WaveNet, Neural2** (e.g. `vi-VN-Neural2-A` female, `-D` male), and newer **Chirp 3 HD** natural voices. Verify the live list with `gcloud text-to-speech voices list --language-code vi-VN` (or the docs) before hard-coding a name.
- **SSML** works on Standard/WaveNet/Neural2 (`<break>`, `<prosody>`, `<say-as>`, and `<sub alias="...">` to force pronunciation of English loanwords like `<sub alias="húc">hook</sub>`). **Chirp 3 HD = most natural but limited/no SSML** — it paces from punctuation.
- **~5,000 bytes per synthesize request.** A per-slide / per-paragraph script chunks naturally under this. For one long continuous file, use the **Long Audio API** (async, writes the result to Cloud Storage).
- Output is **audio only** (MP3 / LINEAR16 wav / OGG) — NOT video. To make a narrated video: synth audio per slide, then combine with the slides/clips in a video editor or with ffmpeg (e.g. imageio-ffmpeg's bundled binary).
- Keep brand/loanwords (Claude Code, hook, skill, Google Cloud, Kubernetes, Telegram, terminal) in English; the neural voice approximates them with a Vietnamese accent — acceptable, or pin pronunciation with `<sub>`.

Context: Claude Hooks & Skills talk; `voiceover-vi.md` in `C:\Users\dvtliem\.claude\docs\hook-present`.
