---
title: "TalkingHead requires offline Blender conversion for VRM avatars"
created: 2026-07-11
type: lesson
status: seedling
source: "virtual-avatar project, 2026-07-11"
tags: [talkinghead, vrm, blender, 3d-avatar, threejs]
---

# TalkingHead requires offline Blender conversion for VRM avatars

TalkingHead (met4citizen/TalkingHead), the JS library used for real-time lip-synced 3D avatars, cannot load VRM files directly and has no runtime/in-browser conversion path — confirmed via its own README and an independent search, not just inferred from an error message.

It requires a Mixamo-compatible bone skeleton plus 52 ARKit blend shapes and 15 Oculus viseme shape keys baked into the mesh. VRM (e.g. VRoid Studio exports) uses an entirely different rig and blend-shape naming convention (Fcl_* shape keys, J_Bip_* bone names), so there is no way to bridge the two formats at runtime — the only supported path is an OFFLINE Blender conversion pass, documented in the project's own blender/VRoid/VROID.md and implemented via several small Python scripts run inside Blender's Scripting workspace plus a custom TalkingHead Blender add-on.

Practical implication: a 'Hips not found' or 'blend shapes not found' error when loading a raw .vrm into TalkingHead is expected/documented behavior, not a bug to chase in the JS layer.

## Related

- [[Blender headless add-on install via bpy.ops.preferences]]
- [[VRM Blender add-on ships classic and Extension zip variants]]
