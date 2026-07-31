---
title: "electron-builder rejects unknown keys in its build config (no comment-key trick)"
created: 2026-07-15
type: lesson
status: seedling
source: "session 2026-07-14"
tags: [electron-builder, config, gotcha, json]
---

# electron-builder rejects unknown keys in its build config (no comment-key trick)

Gotcha (electron-builder 25, 2026-07-14): I added a "_comment_..." key to the `build` config in package.json to document a setting. electron-builder validates its config against a strict JSON schema and FAILS the build with "Invalid configuration object ... has an unknown property '_comment_...'". The "prefix an ignored comment key" trick (works in tsconfig/some tools) does NOT work here — unknown keys are rejected, and package.json is JSON so it can't hold real comments. Keep rationale in commit messages or a separate doc, never as extra keys in the build config. This fails fast at config validation (cheap), before any packaging.
