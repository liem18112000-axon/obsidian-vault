---
ai_hash: dae22c594c4e1702
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-11
entities: []
source: virtual-avatar project, 2026-07-11
status: seedling
tags:
- blender
- automation
- windows
- ci
title: Blender Windows portable builds are plain zips
type: lesson
---

# Blender Windows portable builds are plain zips

Blender's official Windows release builds are distributed as plain portable .zip files at `download.blender.org/release/BlenderX.Y/blender-X.Y.Z-windows-x64.zip` — there's no installer variant needed for automation. Extracting the zip gives a self-contained folder with `blender.exe` at its root; no registry entries, no Start Menu shortcuts, nothing to clean up beyond deleting the folder.

This makes it a good fit for CI or one-off automation scripts that need Blender: download once, cache the zip + extracted folder, and point subprocess calls at that exe path. No admin rights or installer flow required.

Gotcha: the download host (download.blender.org) 403s a bare Python `urllib` request because it rejects the default `Python-urllib/x.y` User-Agent string — see [[download.blender.org rejects default urllib User-Agent]].

## Related

- [[Blender headless add-on install via bpy.ops.preferences]]
- [[download.blender.org rejects default urllib User-Agent]]

%% ai-graph-start %%

**Related notes:**
- [[download.blender.org rejects default urllib User-Agent]]
- [[Blender headless add-on install via bpy.ops.preferences]]

%% ai-graph-end %%