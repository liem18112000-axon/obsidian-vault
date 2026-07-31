---
title: "madjin/vrm-samples repo bundles official VRoid Studio sample avatars with mixed licenses"
created: 2026-07-11
type: observation
status: seedling
source: "github.com/madjin/vrm-samples, session 2026-07-11"
tags: [vroid, vrm, license, avatar, github]
---

# madjin/vrm-samples repo bundles official VRoid Studio sample avatars with mixed licenses

The GitHub repo `madjin/vrm-samples` mirrors official VRoid Studio sample/preset avatar models (anime-style, VRM format) with per-character licensing that is NOT uniform — you cannot treat "it's in this repo" as "it's freely usable." Per the repo's own README (two reference images), the license splits into two groups:

- **CC0 (fully free, copyright waived):** the base male/female uniform models, `HairSample_Male`, `HairSample_Female`, and `AvatarSample_D` through `AvatarSample_G`.
- **"Free with conditions of use" (copyright NOT waived, but alter/distribute permitted):** `AvatarSample_A`, `AvatarSample_B`, `AvatarSample_C` specifically — named explicitly in the README.
- **Undocumented in that README:** the named "character" samples bundled in `vroid/beta/` (Vivi, Vita, Victoria_Rubin, Sendagaya_Shino, Sendagaya_Shibu, Darkness_Shibu, Sakurada_Fumiriya, Avatar_Orion) aren't covered by either license section shown in the README — their status is unverified from this source alone, so treat them as "don't redistribute/use commercially until you find the actual source license" rather than assuming permissiveness just because they sit in the same repo as CC0 files.

Practical lesson: when a GitHub mirror bundles assets from multiple upstream sources, license terms can vary file-by-file even within one folder — check the README's actual attribution images/text per named asset, don't infer from the repo-level LICENSE or from neighboring files.

All of these are VRM format, so none work directly with the `TalkingHead` JS library without Blender conversion first — see [[Avaturn T2 export is a drop-in Ready Player Me replacement for TalkingHead]] for the one format (Avaturn T2 GLB) that needs zero conversion, and TalkingHead's own `blender/VRoid/VROID.md` for the VRM→TalkingHead conversion steps.

## Related

- [[Avaturn T2 export is a drop-in Ready Player Me replacement for TalkingHead]]
- [[Ready Player Me shut down Jan 2026 after Netflix acquisition]]
