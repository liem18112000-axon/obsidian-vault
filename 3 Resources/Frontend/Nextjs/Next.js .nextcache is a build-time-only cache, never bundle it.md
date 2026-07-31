---
title: "Next.js .next/cache is a build-time-only cache, never bundle it"
created: 2026-07-08
type: lesson
status: seedling
source: "vinnstack build optimization session"
tags: [nextjs, build, bundle-size, packaging]
---

# Next.js .next/cache is a build-time-only cache, never bundle it

Next.js writes a persistent webpack/SWC compilation cache to `.next/cache/` during `next build` — its only purpose is to speed up the *next* `next build` invocation (incremental compilation). The actual production server only ever reads `.next/server/`, `.next/static/`, and a handful of small manifest JSON files at the top of `.next/` — `.next/cache` is never touched at runtime.

If a packaging step (Docker image, Electron app via electron-builder, a tarball for deployment) naively globs `.next/**/*` into what it ships, it drags along the entire build cache for no reason. In one Vinnstack (Next.js + Electron) app this cache had grown to 514MB — bigger than the app's actual compiled output (`.next/server` + `.next/static` combined: under 10MB) and bigger than the trimmed production `node_modules`. Fix: add an explicit exclusion glob after the inclusion, e.g. electron-builder's `files: [".next/**/*", "!.next/cache/**/*", ...]` (or the equivalent `.dockerignore` line for a container build).

Related: [[electron-builder files node_modules glob disables devDependency pruning]] — same root cause (a wholesale glob quietly re-including build tooling output instead of ejecting it) as the node_modules devDependency bloat found in the same investigation.

## Related

- [[electron-builder files node_modules glob disables devDependency pruning]]
