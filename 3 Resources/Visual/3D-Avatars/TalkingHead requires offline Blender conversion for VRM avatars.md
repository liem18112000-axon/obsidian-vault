---
ai_hash: 3a25f55330ce0795
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-11
entities: []
source: virtual-avatar project, 2026-07-11
status: seedling
tags:
- talkinghead
- vrm
- blender
- 3d-avatar
- threejs
title: TalkingHead requires offline Blender conversion for VRM avatars
type: lesson
---

# TalkingHead requires offline Blender conversion for VRM avatars

TalkingHead (met4citizen/TalkingHead), the JS library used for real-time lip-synced 3D avatars, cannot load VRM files directly and has no runtime/in-browser conversion path — confirmed via its own README and an independent search, not just inferred from an error message.

It requires a Mixamo-compatible bone skeleton plus 52 ARKit blend shapes and 15 Oculus viseme shape keys baked into the mesh. VRM (e.g. VRoid Studio exports) uses an entirely different rig and blend-shape naming convention (Fcl_* shape keys, J_Bip_* bone names), so there is no way to bridge the two formats at runtime — the only supported path is an OFFLINE Blender conversion pass, documented in the project's own blender/VRoid/VROID.md and implemented via several small Python scripts run inside Blender's Scripting workspace plus a custom TalkingHead Blender add-on.

Practical implication: a 'Hips not found' or 'blend shapes not found' error when loading a raw .vrm into TalkingHead is expected/documented behavior, not a bug to chase in the JS layer.

## Related

- [[Blender headless add-on install via bpy.ops.preferences]]
- [[VRM Blender add-on ships classic and Extension zip variants]]

%% ai-graph-start %%

**Related notes:**
- [[met4citizen TalkingHead is a free browser-native 3D avatar library]]
- [[Avaturn T2 export is a drop-in Ready Player Me replacement for TalkingHead]]
- [[madjinvrm-samples repo bundles official VRoid Studio sample avatars with mixed licenses]]
- [[VRM Blender add-on ships classic and Extension zip variants]]
- [[TalkingHead speakAudio never decodes a single ArrayBuffer before playback]]

%% ai-graph-end %%