---
ai_hash: e204d70c2e515104
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-08
entities: []
source: vinnstack build optimization session
status: seedling
tags:
- electron-builder
- electron
- asar
- native-modules
title: electron-builder asar requires asarUnpack for native .node addons
type: lesson
---

# electron-builder asar requires asarUnpack for native .node addons

A native `.node` addon bundled inside an electron-builder `asar: true` package cannot be loaded at runtime — Node's dlopen/require for native addons needs a real filesystem path, and asar archives are a virtual read-only filesystem that only regular `fs` reads can see through (Electron patches `fs` for that), not OS-level binary loading.

Any package in the app's production dependency tree that ships a `.node` file must be listed in `build.asarUnpack` (a glob, e.g. `"node_modules/@next/swc-*/**/*"`) so electron-builder extracts it next to the asar as real files instead of packing it inside.

Practical way to find every native addon that actually needs unpacking, instead of guessing: `Glob **/*.node` (or `find node_modules -name "*.node"`) across the whole node_modules tree, then cross-check which hits are transitive dependencies of `dependencies` (not `devDependencies` — see [[electron-builder files node_modules glob disables devDependency pruning]], since a devDependency-only native addon like vitest's rollup binaries won't even ship in the packaged app once node_modules pruning is correct). In a Next.js + Electron app the only production-tree native addon was `@next/swc-win32-x64-msvc/next-swc.win32-x64-msvc.node`, a transitive dep of `next` used by its SWC compiler.

## Related

- [[electron-builder files node_modules glob disables devDependency pruning]]

%% ai-graph-start %%

**Related notes:**
- [[electron-builder files node_modules glob disables devDependency pruning]]
- [[Next.js production server never loads the native SWC binary at runtime]]
- [[Cross-building an Electron+Next Windows exe on Linux omits the win32 SWC binary, so the packaged app fails at startup]]
- [[Trim a cross-built Electron exe drop the host-platform native binaries the target never uses (+ maxCompression, one locale)]]
- [[Unsigned asarfalse Electron app ~30s first-launch delay is Defender scanning loose files]]

%% ai-graph-end %%