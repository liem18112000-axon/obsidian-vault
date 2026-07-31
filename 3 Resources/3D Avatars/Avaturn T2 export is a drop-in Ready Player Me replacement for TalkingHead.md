---
title: "Avaturn T2 export is a drop-in Ready Player Me replacement for TalkingHead"
created: 2026-07-11
type: lesson
status: seedling
source: "github.com/met4citizen/TalkingHead/issues/27, session 2026-07-11"
tags: [avaturn, talkinghead, glb, blend-shapes, avatar]
---

# Avaturn T2 export is a drop-in Ready Player Me replacement for TalkingHead

Avaturn (https://avaturn.me) generates GLB avatars compatible with the `met4citizen/TalkingHead` JavaScript lip-sync library, working as a drop-in replacement now that [[Ready Player Me shut down Jan 2026 after Netflix acquisition]]. The key gotcha: Avaturn's editor defaults to exporting the "T1" avatar type, which lacks the blend shapes/visemes TalkingHead needs for lip-sync (`showAvatar()` throws "blend shapes not found"). You must switch the export type to "T2" before downloading — that version includes the necessary blend shapes (ARKit + visemes) and works with zero code changes to TalkingHead, same as an RPM avatar URL would have.

Practical notes from the community (github.com/met4citizen/TalkingHead#27):
- Avaturn does not host the GLB for you (unlike RPM, which served avatars from its own CDN) — you must download the file and host it yourself (e.g. serve it as a static asset in your own app).
- T2 GLB files are noticeably large (~12MB observed).
- No code changes needed in TalkingHead itself — just point the existing avatar-URL config at your self-hosted T2 GLB path instead of a `models.readyplayer.me/*.glb` URL.

## Related

- [[Ready Player Me shut down Jan 2026 after Netflix acquisition]]
