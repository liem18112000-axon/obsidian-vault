---
ai_hash: d9afa3b063c7cd26
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-11
entities: []
source: virtual-avatar session 2026-07-11, static/index.html
status: seedling
tags:
- jsdelivr
- cdn
- versioning
- github
- dependency-management
title: jsdelivr gh CDN can pin to an exact commit SHA, not just tags/branches
type: technique
---

# jsdelivr gh CDN can pin to an exact commit SHA, not just tags/branches

jsdelivr's GitHub-sourced CDN (`https://cdn.jsdelivr.net/gh/<user>/<repo>@<ref>/<path>`) accepts a full commit SHA as `<ref>`, not just a tag or branch name — e.g. `.../gh/met4citizen/TalkingHead@1a7ca9c8a9cd576f2515429ad9f58c540cc421ef/modules/talkinghead.mjs` resolves and serves that exact commit's file content (verified with a 200 response and matching content).

This matters when a library has merged a needed fix/feature to its default branch but hasn't cut a new tagged release yet, and pinning to the branch (e.g. `@main`) would be unacceptably unstable (pulls in every future unreleased change, silently, forever). Pinning to the specific commit SHA that introduces the needed change gives a reproducible, stable reference without waiting for or depending on a formal release — you get exactly the code that existed at that point, forever, the same guarantee a tag would give you, just for an untagged commit.

Concrete case: `met4citizen/TalkingHead`'s last tagged release (`v1.7.0`, published 2025-12-08) predates its Meshopt-compression decoder support, added in commit `1a7ca9c8` on 2026-02-03 — five+ months before any newer tag existed. An avatar sample (`vroid.glb`) needed that support, so the project's import map was pinned directly to that commit SHA instead of `@1.7` (too old, missing the feature) or `@main` (unbounded, unreleased drift). See [[Ready Player Me shut down Jan 2026 after Netflix acquisition]] and [[Avaturn T2 export is a drop-in Ready Player Me replacement for TalkingHead]] for the surrounding avatar-sourcing work this came up in.

## Related

- [[Ready Player Me shut down Jan 2026 after Netflix acquisition]]
- [[Avaturn T2 export is a drop-in Ready Player Me replacement for TalkingHead]]

%% ai-graph-start %%

**Related notes:**
- [[Avaturn T2 export is a drop-in Ready Player Me replacement for TalkingHead]]
- [[met4citizen TalkingHead is a free browser-native 3D avatar library]]
- [[TalkingHead speakAudio never decodes a single ArrayBuffer before playback]]

%% ai-graph-end %%