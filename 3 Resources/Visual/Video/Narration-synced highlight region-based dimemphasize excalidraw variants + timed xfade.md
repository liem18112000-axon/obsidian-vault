---
title: "Narration-synced highlight: region-based dim/emphasize excalidraw variants + timed xfade"
created: 2026-06-18
type: howto
status: seedling
source: "session 2026-06-18"
tags: [ffmpeg, excalidraw, video, xfade, highlight, tts, gotcha]
---

# Narration-synced highlight: region-based dim/emphasize excalidraw variants + timed xfade

How to make on-screen content highlight in sync with what an AI voiceover is saying, for diagram-style slides — without per-element timing data or a forced aligner.

## Approach: dim & emphasize, region-based, timed proportionally
1. **Emphasis variants per slide.** For each structured diagram (columns / cards), define the section rectangles in the diagram's own canvas coords. A generic post-processor reads the base .excalidraw and, for each section k, emits a variant where every element whose CENTER falls inside *another* section's rect gets `opacity` lowered (~18); the active section + context elements (title/banner/arrows outside all sections) stay at 100. Region-based membership means no per-element tagging and no coordinate mapping to the final frame — emphasis is baked in the diagram's own render, so it can't drift against a Ken Burns zoom.
2. **Render each variant to PNG.**
3. **Sequence with timing = the slide's narration.** Auto-proportional: split the slide duration evenly across N sections (the script already narrates sections in order, so even split lands within ~1-2s). Build the segment as an **xfade chain** over the N stills: each still length `L = (dur + (n-1)*T)/n`, transition `T~0.45`, k-th xfade `offset = k*(L-T)`; total == dur. Map the narration audio, add edge fades, no Ken Burns on these slides (the emphasis IS the motion).

## Gotcha: the excalidraw renderer truncates filenames at the first dot
`render_excalidraw.py foo.emph1.excalidraw` writes `foo.png` (drops everything after the first '.') — so multi-dot variant names all collide onto one PNG and can even overwrite the real base. Use a single dot (extension only): name variants `foo_emph1.excalidraw` (underscore), not `foo.emph1.excalidraw`.

## Apply to multiple languages
Keep emphasis PNGs, slides, and embedded clips language-agnostic; parameterize only the voiceover file + TTS voice + narration cache dir + output name by a LANG_CODE. The same `en-US-Chirp3-HD-<Name>` / `vi-VN-Chirp3-HD-<Name>` voice name exists across locales, so you keep the same 'presenter' across languages.

Context: `C:\Users\dvtliem\.claude\docs\hook-present\build` (emphasize.js + make-narrated-video.py).
