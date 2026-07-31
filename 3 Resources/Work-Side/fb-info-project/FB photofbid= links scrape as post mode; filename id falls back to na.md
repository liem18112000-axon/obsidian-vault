---
title: "FB /photo/?fbid= links scrape as post mode; filename id falls back to na"
created: 2026-06-14
type: observation
status: seedling
source: "live run 2026-06-14"
tags: [fb-info-project, facebook, url-parsing, gotcha]
---

# FB /photo/?fbid= links scrape as post mode; filename id falls back to na

In fb-info-project, a Facebook photo link (`https://www.facebook.com/photo/?fbid=<id>`) is **not** handled specially — `src/urls.detect_source` has no `/photo/` branch, so it falls through the reel/profile/video checks to the default `post` mode. The photo's comment thread is then scraped exactly like a post's: collect commenters → visit their profiles.

Side effect on the output filename: `extract_post_id` only matches `/reel/`, `/videos?/`, `story_fbid=`, `/posts/`, and `[?&]id=` — **not** `fbid=`. So a photo link yields no id and the output file is named `<stt>_post_na_<stamp>.xlsx` (the literal 'na' fallback). If photo links should get a real id in the filename, add an `fbid=(\d+)` pattern to `extract_post_id` (and optionally a `/photo/` -> dedicated handling in `detect_source`).

Confirmed by a live run on 2026-06-14: `/photo/?fbid=2218458972230714` -> `output/1_post_na_...xlsx`.

## Related

- [[3 Resources/Work-Side/fb-info-project/Stale FB session signature login popup + profile 302 to login + empty location columns]]
