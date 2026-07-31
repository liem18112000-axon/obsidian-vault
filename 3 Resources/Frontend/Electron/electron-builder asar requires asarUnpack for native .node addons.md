---
title: "electron-builder asar requires asarUnpack for native .node addons"
created: 2026-07-08
type: lesson
status: seedling
source: "vinnstack build optimization session"
tags: [electron-builder, electron, asar, native-modules]
---

# electron-builder asar requires asarUnpack for native .node addons

A native `.node` addon bundled inside an electron-builder `asar: true` package cannot be loaded at runtime — Node's dlopen/require for native addons needs a real filesystem path, and asar archives are a virtual read-only filesystem that only regular `fs` reads can see through (Electron patches `fs` for that), not OS-level binary loading.

Any package in the app's production dependency tree that ships a `.node` file must be listed in `build.asarUnpack` (a glob, e.g. `"node_modules/@next/swc-*/**/*"`) so electron-builder extracts it next to the asar as real files instead of packing it inside.

Practical way to find every native addon that actually needs unpacking, instead of guessing: `Glob **/*.node` (or `find node_modules -name "*.node"`) across the whole node_modules tree, then cross-check which hits are transitive dependencies of `dependencies` (not `devDependencies` — see [[electron-builder files node_modules glob disables devDependency pruning]], since a devDependency-only native addon like vitest's rollup binaries won't even ship in the packaged app once node_modules pruning is correct). In a Next.js + Electron app the only production-tree native addon was `@next/swc-win32-x64-msvc/next-swc.win32-x64-msvc.node`, a transitive dep of `next` used by its SWC compiler.

## Related

- [[electron-builder files node_modules glob disables devDependency pruning]]
