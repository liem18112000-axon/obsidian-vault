---
ai_hash: fff0efd98969bb73
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-08
entities: []
source: vinnstack build optimization session
status: seedling
tags:
- nextjs
- build
- bundle-size
- packaging
title: Next.js .next/cache is a build-time-only cache, never bundle it
type: lesson
---

# Next.js .next/cache is a build-time-only cache, never bundle it

Next.js writes a persistent webpack/SWC compilation cache to `.next/cache/` during `next build` — its only purpose is to speed up the *next* `next build` invocation (incremental compilation). The actual production server only ever reads `.next/server/`, `.next/static/`, and a handful of small manifest JSON files at the top of `.next/` — `.next/cache` is never touched at runtime.

If a packaging step (Docker image, Electron app via electron-builder, a tarball for deployment) naively globs `.next/**/*` into what it ships, it drags along the entire build cache for no reason. In one Vinnstack (Next.js + Electron) app this cache had grown to 514MB — bigger than the app's actual compiled output (`.next/server` + `.next/static` combined: under 10MB) and bigger than the trimmed production `node_modules`. Fix: add an explicit exclusion glob after the inclusion, e.g. electron-builder's `files: [".next/**/*", "!.next/cache/**/*", ...]` (or the equivalent `.dockerignore` line for a container build).

Related: [[electron-builder files node_modules glob disables devDependency pruning]] — same root cause (a wholesale glob quietly re-including build tooling output instead of ejecting it) as the node_modules devDependency bloat found in the same investigation.

## Related

- [[electron-builder files node_modules glob disables devDependency pruning]]

%% ai-graph-start %%

**Related notes:**
- [[electron-builder files node_modules glob disables devDependency pruning]]
- [[Two next dev instances sharing one .next corrupt the webpack PackFileCache]]
- [[Trim a cross-built Electron exe drop the host-platform native binaries the target never uses (+ maxCompression, one locale)]]
- [[Next.js dev server webpack chunk cache corrupts after many route addsdeletes]]
- [[Next.js production server never loads the native SWC binary at runtime]]

%% ai-graph-end %%