---
title: "download.blender.org rejects default urllib User-Agent"
created: 2026-07-11
type: lesson
status: seedling
source: "virtual-avatar project, 2026-07-11"
tags: [python, urllib, http, gotcha]
---

# download.blender.org rejects default urllib User-Agent

A plain `urllib.request.urlretrieve()`/`urlopen()` call from Python gets HTTP 403 Forbidden from download.blender.org, because that host rejects requests carrying the default `Python-urllib/x.y` User-Agent header. Adding an explicit `User-Agent` header (a plain browser-like string such as `Mozilla/5.0` is enough — the check appears to just be blocking the default urllib UA, not doing real browser fingerprinting) makes the request succeed.

General lesson: when a download from a normal public file host 403s with no other explanation, suspect User-Agent filtering before suspecting auth/permissions — many CDNs and static hosts block default HTTP-library user agents as a crude bot filter.

## Related

- [[Blender Windows portable builds are plain zips]]
