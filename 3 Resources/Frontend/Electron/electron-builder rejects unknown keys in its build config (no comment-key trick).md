---
ai_hash: 90089e954f899881
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-15
entities: []
source: session 2026-07-14
status: seedling
tags:
- electron-builder
- config
- gotcha
- json
title: electron-builder rejects unknown keys in its build config (no comment-key trick)
type: lesson
---

# electron-builder rejects unknown keys in its build config (no comment-key trick)

Gotcha (electron-builder 25, 2026-07-14): I added a "_comment_..." key to the `build` config in package.json to document a setting. electron-builder validates its config against a strict JSON schema and FAILS the build with "Invalid configuration object ... has an unknown property '_comment_...'". The "prefix an ignored comment key" trick (works in tsconfig/some tools) does NOT work here — unknown keys are rejected, and package.json is JSON so it can't hold real comments. Keep rationale in commit messages or a separate doc, never as extra keys in the build config. This fails fast at config validation (cheap), before any packaging.

%% ai-graph-start %%

**Related notes:**
- [[electron-builder --win CLI flag overrides win.target in package.json]]
- [[electron-builder files node_modules glob disables devDependency pruning]]

%% ai-graph-end %%