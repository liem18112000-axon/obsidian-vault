---
ai_hash: 8f8a2d6356453935
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-07
entities: []
source: vinnstack cloudbuild.yaml setup, 2026-07-07
status: seedling
tags:
- electron
- electron-builder
- cloud-build
- ci
- wine
title: Cross-building Electron Windows exe on Linux needs wine
type: lesson
---

# Cross-building Electron Windows exe on Linux needs wine

Electron-builder'\''s `--win` target (including the `portable` target) cannot cross-compile from a plain Linux `node:*` Cloud Build/CI image — it needs Wine (to run Windows-only packaging tools) and rcedit (to stamp the exe'\''s icon/metadata), neither of which ship in standard Node images.

The fix is to run the packaging step in a Wine-enabled image such as `electronuserland/builder:wine` (Docker Hub), which bundles Node + Wine + the toolchain electron-builder needs for Windows targets. Run `npm ci` fresh inside that image rather than reusing `node_modules` from an earlier CI step on a different base image, since native/postinstall artifacts (like electron'\''s downloaded binaries) can be image-specific.

## Related
[[Vinnstack bundles cloud-sql-proxy.exe as a gitignored extraResource]]
[[Publish arbitrary binaries to Artifact Registry with a generic repo]]

%% ai-graph-start %%

**Related notes:**
- [[electron-builder --win CLI flag overrides win.target in package.json]]
- [[Cross-building an Electron+Next Windows exe on Linux omits the win32 SWC binary, so the packaged app fails at startup]]
- [[Vinnstack bundles cloud-sql-proxy.exe as a gitignored extraResource]]
- [[gcloud components install fetches the host's own platform binary, not a chosen target]]
- [[Trim a cross-built Electron exe drop the host-platform native binaries the target never uses (+ maxCompression, one locale)]]

%% ai-graph-end %%