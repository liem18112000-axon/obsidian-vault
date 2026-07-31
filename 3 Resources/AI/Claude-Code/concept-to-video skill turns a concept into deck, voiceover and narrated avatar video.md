---
title: "concept-to-video skill turns a concept into deck, voiceover and narrated avatar video"
created: 2026-06-19
type: reference
status: seedling
source: "session 2026-06-19"
tags: [skill, claude-code, video, slides, tts, reference]
---

# concept-to-video skill turns a concept into deck, voiceover and narrated avatar video

Reusable skill at `~/.claude/skills/concept-to-video/` that runs the whole chain from a concept + resources: excalidraw diagrams -> pptx deck -> per-slide voiceover (EN + humorous Southern-VI) -> staged-reveal videos -> narrated HD video (Google TTS) -> audio-reactive anime-mascot presenter overlay. It generalizes the three hand-built decks under `~/.claude/docs/{hook,obsidian,telegram}-present`, which stay as the reference outputs.

Structure:
- `SKILL.md` - the 8-step workflow.
- `setup.sh` - one-time pre-step; checks/fixes node+pptxgenjs, python PIL/fitz/imageio-ffmpeg/python-pptx, LibreOffice, uv + excalidraw renderer, gcloud TTS (with a LIVE synth test), avatar images, and the four Windows fonts.
- `templates/` - config-driven `make-narrated-video.py` + `add-avatar.py` (both driven by a per-deck `deck.config.json`), generic `make-video.py` + `_frames.js`, and copy-and-adapt `gen-diagram` + `build-deck` templates.
- `references/` - `pipeline.md`, `diagram-and-deck-rules.md`, `voiceover-and-tts.md` (the gotchas).

Per-deck flow: run `setup.sh` once; create `docs/<topic>-present/` with `build,diagrams,assets`; adapt the gen + build-deck templates; write `voiceover-vi/en.md` as `[Slide N]` blocks; write `deck.config.json`; then `DECK=<dir> LANG_CODE=VI python make-narrated-video.py` followed by `add-avatar.py`.

## Related

- [[Make one diagram generator double as a reveal-video frame source with STAGE() markers]]
- [[3 Resources/Cloud/GCP/Google Cloud TTS from Windows fetch the token in bash, pass via env to Python]]
- [[Audio-reactive anime mascot overlay for narrated videos (ffmpeg)]]
