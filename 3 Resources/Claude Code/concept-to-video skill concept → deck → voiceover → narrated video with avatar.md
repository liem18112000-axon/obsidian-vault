---
title: "concept-to-video skill: concept → deck → voiceover → narrated video with avatar"
created: 2026-06-19
type: reference
status: seedling
source: "session 2026-06-19"
tags: [skill, claude-code, video, slides, tts, reference]
---

# concept-to-video skill: concept → deck → voiceover → narrated video with avatar

Reusable skill at ~/.claude/skills/concept-to-video/ that turns a concept + resources into the full chain: excalidraw diagrams → pptx deck → per-slide voiceover (EN + humorous Southern-VI) → staged-reveal videos → narrated HD video (Google TTS) → + audio-reactive anime-mascot presenter. It is the generalization of the three hand-built decks under ~/.claude/docs/{hook,obsidian,telegram}-present (the reference outputs).

Structure: SKILL.md (8-step workflow), setup.sh (one-time pre-step that checks/fixes node+pptxgenjs, python PIL/fitz/imageio-ffmpeg/python-pptx, LibreOffice, uv+excalidraw renderer, gcloud TTS with a LIVE synth test, avatar images, and the four Windows fonts), templates/ (config-driven make-narrated-video.py + add-avatar.py driven by a per-deck deck.config.json; generic make-video.py + _frames.js; copy-and-adapt gen-diagram + build-deck templates), references/ (pipeline.md, diagram-and-deck-rules.md, voiceover-and-tts.md — all the gotchas).

Per-deck flow: run setup.sh once; make a docs/<topic>-present/ with build,diagrams,assets; adapt the gen + build-deck templates; write voiceover-vi/en.md as [Slide N] blocks; write deck.config.json; then DECK=<dir> LANG_CODE=VI python make-narrated-video.py and add-avatar.py. Relates to [[Make one diagram generator double as a reveal-video frame source with STAGE() markers]], [[Google Cloud TTS from Windows: fetch the token in bash, pass via env to Python]], [[Audio-reactive anime mascot overlay for narrated videos (ffmpeg)]].

## Related

- [[Make one diagram generator double as a reveal-video frame source with STAGE() markers]]
- [[Google Cloud TTS from Windows: fetch the token in bash]]
- [[pass via env to Python]]
- [[Audio-reactive anime mascot overlay for narrated videos (ffmpeg)]]
