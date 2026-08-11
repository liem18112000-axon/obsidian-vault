---
ai_hash: 4eb38dd43e890092
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-19
entities: []
source: session 2026-06-19
status: seedling
tags:
- youtube
- thumbnail
- media
- gotcha
title: Grab a clean YouTube thumbnail via img.youtube.com/vi/<id>/maxresdefault.jpg
type: howto
---

# Grab a clean YouTube thumbnail via img.youtube.com/vi/<id>/maxresdefault.jpg

To use a YouTube video as a clean still 'example' image, don't screenshot the player (you get pre-roll ads and chrome). Download the thumbnail directly: `https://img.youtube.com/vi/<VIDEO_ID>/maxresdefault.jpg` (1280x720). If that 404s it returns a tiny (<8 KB) placeholder — fall back to `hqdefault.jpg` (480x360). The video id is the `v=` param in the watch URL. Convert to PNG if your downstream size-reader only parses PNG IHDR.

%% ai-graph-start %%

**Related notes:**
- _(none above threshold)_

%% ai-graph-end %%