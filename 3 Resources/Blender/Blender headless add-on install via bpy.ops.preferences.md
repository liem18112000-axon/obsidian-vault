---
title: "Blender headless add-on install via bpy.ops.preferences"
created: 2026-07-11
type: howto
status: seedling
source: "virtual-avatar project, 2026-07-11"
tags: [blender, automation, headless, bpy, cli]
---

# Blender headless add-on install via bpy.ops.preferences

A classic (non-Extension-format) Blender add-on can be installed and enabled fully headlessly, with no UI, using `bpy.ops.preferences.addon_install(filepath=zip_path, overwrite=True)` followed by `bpy.ops.preferences.addon_enable(module="module_name")`, where module_name is the add-on's root package folder name inside the zip (e.g. `io_scene_vrm` for the VRM importer).

This makes it possible to script an entire Blender pipeline — install add-on, import a file, run bpy operations, export — via a single command: `blender --background --factory-startup --python script.py -- arg1 arg2`. Inside script.py, the args after the `--` separator are found via `sys.argv.index("--")`; everything in sys.argv before that index belongs to Blender's own CLI parsing, not the script.

Many of the operator classes an add-on defines (things you'd normally click as buttons in a sidebar panel) wrap a plain function underneath — check the operator's `execute()` body for the actual function call and invoke that function directly instead of the operator, to sidestep operator `poll()`/context requirements that can be finicky in background mode.

## Related

- [[Blender Windows portable builds are plain zips]]
- [[TalkingHead requires offline Blender conversion for VRM avatars]]
