---
title: "HTML-rendered chat demo videos: serve over http, cumulative screenshots, pre-pad dark"
created: 2026-06-19
type: howto
status: seedling
source: "session 2026-06-19"
tags: [playwright, html, video, ffmpeg, demo]
---

# HTML-rendered chat demo videos: serve over http, cumulative screenshots, pre-pad dark

To make a per-feature 'demo video' that is a genuine HTML render (e.g. command-by-command Telegram chat demos), not a PIL mockup:

1. Build ONE self-contained chat.html that renders a scenario from a JS object, controlled by URL params (e.g. ?cmd=new&upto=K shows the first K bubbles). Style it like the target app (Telegram dark). Native browser = real color emoji, crisp CSS.
2. **Serve it over http** — Playwright MCP BLOCKS the file:// protocol. Run `python -m http.server 8777` in the dir and navigate to http://localhost:8777/chat.html?...
3. Capture cumulative-reveal frames: set a FIXED viewport (browser_resize, e.g. 920x760) and take VIEWPORT (not fullPage) screenshots so every frame is the same size and top-aligned — then crossfades between steps don't jump vertically. Two frames per item (typed -> result) is enough; make-video.py crossfades them into a ~5s demo.
4. **Dark-content gotcha:** make-video.py letterbox-pads to 1920x1080 with WHITE. A dark chat frame then gets ugly white bars. Fix: pre-pad each captured frame onto a 1920x1080 DARK canvas (PIL, scale-to-fit + center) BEFORE make-video, so the frame already fills 16:9 and no white shows. Then extract the poster with ffmpeg -sseof -0.3.

Used for the telegram deck's /new /sessions /status /cancel /approvals demo slides. Relates to [[Make one diagram generator double as a reveal-video frame source with STAGE() markers]], [[concept-to-video skill turns a concept into deck, voiceover and narrated avatar video]].

## Related

- [[concept-to-video skill turns a concept into deck, voiceover and narrated avatar video]]
