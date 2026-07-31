---
title: "VRM Blender add-on ships classic and Extension zip variants"
created: 2026-07-11
type: lesson
status: seedling
source: "virtual-avatar project, 2026-07-11"
tags: [blender, vrm, addon, automation]
---

# VRM Blender add-on ships classic and Extension zip variants

The community VRM importer/exporter for Blender (saturday06/VRM-Addon-for-Blender on GitHub) publishes two zip assets per release: a classic add-on zip (`VRM_Addon_for_Blender-X_Y_Z.zip`) installable via Blender's classic `bpy.ops.preferences.addon_install`/`addon_enable` API, and an `-Extension-` zip built for Blender 4.2+'s newer Extensions platform (installed differently, via the extensions package manager).

For scripted/headless installs, the classic zip is the simpler target: the classic preferences API (`addon_install`/`addon_enable`) has been stable across many Blender versions, whereas the Extensions platform API is newer and Blender-version-sensitive. The classic add-on's module name (needed for `addon_enable(module=...)`) is `io_scene_vrm` — matches the source folder name in the repo (`src/io_scene_vrm`).

## Related

- [[TalkingHead requires offline Blender conversion for VRM avatars]]
- [[Blender headless add-on install via bpy.ops.preferences]]
