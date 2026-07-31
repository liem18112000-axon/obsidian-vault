---
ai_hash: 768a3772f7750171
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-11
entities: []
source: virtual-avatar project, 2026-07-11
status: seedling
tags:
- blender
- automation
- addon
- gotcha
title: Blender release-zip addon module name may not match the source package name
type: lesson
---

# Blender release-zip addon module name may not match the source package name

When installing a Blender add-on programmatically via `bpy.ops.preferences.addon_install()` + `addon_enable(module=...)`, the module name to pass isn't reliably the package name you'd read from the project's GitHub source tree (e.g. `src/io_scene_vrm`) — it's whatever top-level folder name the packaged release zip actually contains, which a build/release script can rename (for VRM-Addon-for-Blender's v4.3.3 release, that's `VRM_Addon_for_Blender-release`, not `io_scene_vrm`).

Fix: after `addon_install`, don't hardcode the module name — discover it by scanning Blender's addons directory (`bpy.utils.user_resource("SCRIPTS", path="addons")`) for the newly extracted folder, and pass that literal folder name to `addon_enable`. This makes the install step resilient to the add-on's packaging changing its folder name across releases. See [[Blender headless add-on install via bpy.ops.preferences]].

## Related

- [[Blender headless add-on install via bpy.ops.preferences]]
- [[VRM Blender add-on ships classic and Extension zip variants]]

%% ai-graph-start %%

**Related notes:**
- [[VRM Blender add-on ships classic and Extension zip variants]]
- [[Blender headless add-on install via bpy.ops.preferences]]

%% ai-graph-end %%