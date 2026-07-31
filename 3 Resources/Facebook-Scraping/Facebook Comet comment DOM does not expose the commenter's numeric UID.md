---
title: "Facebook Comet comment DOM does not expose the commenter's numeric UID"
created: 2026-07-26
type: observation
status: seedling
source: "fb-info-project live probe 2026-07-26"
tags: [facebook, osint, scraping, uid, fb-info-project]
---

# Facebook Comet comment DOM does not expose the commenter's numeric UID

On the current Facebook web UI ("Comet"), the commenter's numeric UID is **not present in the comment DOM** — you cannot harvest it while scanning comments; you must visit the profile.

Verified with a read-only probe of 30 real comment/reply articles on a live post (2026-07-26):

- **0 of 30** articles contained any numeric-UID token (`fb://profile/`, `/user.php?id=`, or `actor` JSON) anywhere in the article's own HTML.
- The author link is one of three shapes:
  - **vanity handle** (~81%, e.g. `/mai.buithanh.5621`) — no id anywhere in the URL.
  - **`/people/<Name>/pfbid…/`** (~15%) — the `pfbid` is an opaque privacy token, **not** the numeric id; it only resolves by visiting the profile.
  - **`/people/<Name>/<NUMERIC>/`** (~4%) — here the numeric UID sits right in the URL path, equivalent to `profile.php?id=<id>`.

**Gotcha:** the author link's `?comment_id=` param base64-decodes to `comment:<post_fbid>_<comment_numeric_id>` — that is the *comment's* id, not the author's UID. Easy to mistake for a user id; it isn't one.

**Consequence (how to actually get a vanity commenter's UID):** visit the profile page and read the `fb://profile/<id>` app-link meta (`al:android:url` / `al:ios:url`). That meta describes the page's *subject* (the owner). Do **not** trust `"userID"` in page JSON — that is the logged-in *viewer's* id, identical on every page. The `mbasic.facebook.com` `lst=<viewer>:<owner>:<ts>` token (middle value = owner) is a fallback, but mbasic is being sunset by Facebook.

**Free win:** the `/people/<name>/<digits>/` URL form gives the UID with no fetch — parse it from the URL exactly like `profile.php?id=`. In fb-info-project this is done by `src/urls.py::uid_from_url`, which `extract_uid` and `profiles.py` both use to skip fetching page HTML / the mbasic fallback.
