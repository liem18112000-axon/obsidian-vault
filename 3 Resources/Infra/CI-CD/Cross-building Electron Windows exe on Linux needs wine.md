---
title: "Cross-building Electron Windows exe on Linux needs wine"
created: 2026-07-07
type: lesson
status: seedling
source: "vinnstack cloudbuild.yaml setup, 2026-07-07"
tags: [electron, electron-builder, cloud-build, ci, wine]
---

# Cross-building Electron Windows exe on Linux needs wine

Electron-builder'\''s `--win` target (including the `portable` target) cannot cross-compile from a plain Linux `node:*` Cloud Build/CI image — it needs Wine (to run Windows-only packaging tools) and rcedit (to stamp the exe'\''s icon/metadata), neither of which ship in standard Node images.

The fix is to run the packaging step in a Wine-enabled image such as `electronuserland/builder:wine` (Docker Hub), which bundles Node + Wine + the toolchain electron-builder needs for Windows targets. Run `npm ci` fresh inside that image rather than reusing `node_modules` from an earlier CI step on a different base image, since native/postinstall artifacts (like electron'\''s downloaded binaries) can be image-specific.

## Related
[[Vinnstack bundles cloud-sql-proxy.exe as a gitignored extraResource]]
[[Publish arbitrary binaries to Artifact Registry with a generic repo]]
