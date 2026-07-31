---
title: "Vietnamese Google Cloud TTS: write AI as trí tuệ nhân tạo, chunk per request, audio not video"
created: 2026-06-18
type: lesson
status: seedling
source: "sessions 2026-06-18 / 2026-06-19"
tags: [google-cloud, gcp, tts, text-to-speech, vietnamese, ssml, gotcha]
---

# Vietnamese Google Cloud TTS: write AI as trí tuệ nhân tạo, chunk per request, audio not video

Narrating a Vietnamese script with **Google Cloud Text-to-Speech** (`vi-VN`).

**The big gotcha:** a `vi-VN` voice reads the letters **AI** as the Vietnamese word *ai* ("who"), and **A.I.** as *a chấm i chấm*. Confirmed on the Chirp 3 HD voice `vi-VN-Chirp3-HD-Leda`. Replace AI / A.I. in the script with **"trí tuệ nhân tạo"** (the field) or **"trợ lý"** (the assistant); same care for any other dotted acronym. Keep genuine brand/loanwords in English (Claude, Obsidian, Quartz, Gemini, Vertex, GitHub, Markdown, hook, skill, Kubernetes, Telegram) — the neural voice approximates them with a Vietnamese accent, or pin pronunciation with `<sub>`.

Capabilities / limits:
- Voices: **Standard, WaveNet, Neural2** (`vi-VN-Neural2-A` female, `-D` male) and **Chirp 3 HD**. Verify the live list with `gcloud text-to-speech voices list --language-code vi-VN` before hard-coding a name.
- **SSML** works on Standard/WaveNet/Neural2 (`<break>`, `<prosody>`, `<say-as>`, `<sub alias="húc">hook</sub>`). **Chirp 3 HD is the most natural but has limited/no SSML** — it paces from punctuation.
- **~5,000 bytes per `synthesize` request.** Per-slide / per-paragraph chunking fits naturally; for one long continuous file use the **Long Audio API** (async, writes to Cloud Storage).
- Output is **audio only** (MP3 / LINEAR16 / OGG), never video — synth per slide, then combine with ffmpeg or an editor.

Context: Claude Hooks & Skills talk; `voiceover-vi.md` in `C:\Users\dvtliem\.claude\docs\hook-present`.

## Related

- [[3 Resources/Cloud/GCP/Google Cloud TTS from Windows fetch the token in bash, pass via env to Python]]
