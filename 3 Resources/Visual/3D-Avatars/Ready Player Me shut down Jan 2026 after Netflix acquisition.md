---
title: "Ready Player Me shut down Jan 2026 after Netflix acquisition"
created: 2026-07-11
type: observation
status: seedling
source: "web research 2026-07-11: TechCrunch, Variety, RoadToVR, github.com/met4citizen/TalkingHead/issues/27"
tags: [ready-player-me, netflix, shutdown, glb, avatar, talkinghead]
---

# Ready Player Me shut down Jan 2026 after Netflix acquisition

Netflix acquired Ready Player Me (the free 3D avatar-creation platform at readyplayer.me, widely used for VRM/GLB avatars with facial blend shapes) in December 2025, and Ready Player Me shut down all public-facing services — including its avatar creator and the newer "PlayerZero" platform — on January 31, 2026. The `readyplayer.me` domain no longer resolves at all (DNS_PROBE_FINISHED_NXDOMAIN), so any project or tutorial referencing a `models.readyplayer.me/*.glb` URL is now permanently broken, not just experiencing an outage.

Confirmed across independent sources: TechCrunch, Variety, and RoadToVR news coverage of the acquisition, plus first-hand discussion in the maintainer-run GitHub issue [met4citizen/TalkingHead#27](https://github.com/met4citizen/TalkingHead/issues/27).

Practical implication: any app built on a "paste your Ready Player Me avatar URL here" flow (e.g. the `TalkingHead` JS lip-sync library) needs a replacement avatar source now. See [[Avaturn T2 export is a drop-in Ready Player Me replacement for TalkingHead]] for the confirmed working alternative.

## Related

- [[Avaturn T2 export is a drop-in Ready Player Me replacement for TalkingHead]]
