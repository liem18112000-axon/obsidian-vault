---
title: "Assemble a narrated slide video: pptx to png + per-slide Google TTS + ffmpeg -shortest segments + concat"
created: 2026-06-18
type: howto
status: seedling
source: "session 2026-06-18"
tags: [ffmpeg, video, tts, google-cloud, pymupdf, libreoffice, pptx, slideshow]
---

# Assemble a narrated slide video: pptx to png + per-slide Google TTS + ffmpeg -shortest segments + concat

End-to-end recipe to turn a slide deck + a per-slide narration script into one narrated HD video — entirely local except the TTS call. (Built the Claude Hooks & Skills talk: 28 slides, ~17.5 min, 1920x1080 h264 + AAC, ~37 MB.)

## Steps
1. **Narration → audio, per slide.** Parse the voiceover .md into one block per `[Slide N]`. Synth each with Google Cloud TTS REST (`v1/text:synthesize`, voice e.g. `vi-VN-Chirp3-HD-Charon`, `LINEAR16`), decode base64 `audioContent` to a wav. Token via `gcloud auth print-access-token` (fetch in bash, pass to Python through an env var — Python's subprocess can't run the `gcloud.cmd` shim on Windows). Send header `x-goog-user-project: <project>`. Make it idempotent (skip existing wavs) so re-runs don't re-bill.
2. **Slides → 1080p PNGs.** LibreOffice `soffice --headless --convert-to pdf`, then PyMuPDF render each page: `zoom = 1920 / page.rect.width`, `get_pixmap(Matrix(zoom,zoom))` → 1920x1080 for a 16:9 deck. (The `MuPDF error: No common ancestor in structure tree` lines are harmless.)
3. **Per-slide segment.** For a static slide, let the **audio drive the length** — no duration math needed:
   `ffmpeg -loop 1 -i slide.png -i slide.wav -c:v libx264 -r 30 -pix_fmt yuv420p -vf "scale=1920:1080:force_original_aspect_ratio=decrease,pad=1920:1080:(ow-iw)/2:(oh-ih)/2:white,setsar=1" -c:a aac -ar 48000 -ac 2 -b:a 192k -shortest seg.mp4` (`-shortest` ends with the audio; the looped image fills it).
4. **Video slides** (clips embedded in the deck): use the real clip, freeze its last frame to cover narration that outlasts it, audio-driven length:
   `-filter_complex "[0:v]<same scale/pad>,fps=30,tpad=stop_mode=clone:stop_duration=120[v]" -map [v] -map 1:a ... -shortest`.
5. **Concat** uniform segments with the demuxer + copy: a `concat.txt` of `file 'seg-NN.mp4'` lines → `ffmpeg -f concat -safe 0 -i concat.txt -c copy out.mp4`. Works because every segment was encoded with identical params; fall back to re-encode if copy errors.

## Why this shape
- `-shortest` + looped still removes all per-clip duration bookkeeping.
- Uniform encode settings across segments make concat `-c copy` instant and glitch-free.
- imageio-ffmpeg's bundled binary means no system ffmpeg needed (`imageio_ffmpeg.get_ffmpeg_exe()`).
- Static 1080p slideshow compresses tiny (~100 kb/s video) yet stays crisp because each slide is one still.

Context / script: `C:\Users\dvtliem\.claude\docs\hook-present\build\make-narrated-video.py`.

## Add tasteful motion (dynamic but non-distracting)
- **Ken Burns** on static slides: render the slide PNG at **2x** (e.g. PyMuPDF zoom = 3840/page.width) so the zoom stays crisp, then `zoompan=z='min(1.0+0.06*on/F,1.06)':d=F:x='iw/2-(iw/zoom/2)':y='ih/2-(ih/zoom/2)':s=1920x1080:fps=30` where `F = round(duration*30)`. A gentle ~6% zoom reads as "alive" without distracting.
- **Soft dissolves without a 28-way xfade graph:** give each segment a `fade=t=in:d=0.3:color=white` and `fade=t=out:st=DUR-0.3:d=0.3:color=white`, then plain demuxer concat. Because the slide backgrounds are white, fade-out-to-white into fade-in-from-white looks like a clean cross-dissolve — and it's far more robust than chaining `xfade` across many clips. Use `color=black` on the very first slide's fade-in and last slide's fade-out for a film-style open/close.
- Get each segment's duration from the narration WAV with Python's `wave` module (`getnframes()/getframerate()`) — no ffprobe needed — and pass it as `-t`.
- Cost: motion makes frames change, so the file is larger / higher bitrate than a static slideshow (here ~108 MB / ~990 kb/s vs ~37 MB for the static cut) — expected, and the quality is better.
- Vietnamese voice note: Google `vi-VN` voices are **Northern accent** only (Chirp3-HD `Leda` = lively female used here); a humorous *Southern* feel comes from word choice in the script, not the voice. True Saigon accent needs FPT.AI / Zalo / VBee TTS. See [[3 Resources/Cloud/GCP/text-to-speech/Vietnamese Google Cloud TTS write AI as trí tuệ nhân tạo, chunk per request, audio not video]].
