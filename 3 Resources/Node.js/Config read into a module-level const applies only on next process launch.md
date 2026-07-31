---
title: "Config read into a module-level const applies only on next process launch"
created: 2026-07-02
type: lesson
status: seedling
source: "session 2026-07-02"
tags: [config, nextjs, module-load, gotcha, vinnstack]
---

# Config read into a module-level const applies only on next process launch

A value captured into a module-level `const` at import time (e.g. `export const CLAUDE_MODEL = getConfig().anthropicModel` in Vinnstack's ultracodeRunner) is frozen for the life of that module instance. Persisting a new value to the config file at runtime (via a Settings POST) updates the file and reloadConfig()'s cache, but the already-evaluated const does NOT change — the running process keeps the old value. So a "change the model in Settings" feature must tell the user it takes effect on the NEXT app launch, or the code must read getConfig() lazily (per use) instead of once at module top. Rule of thumb: settings that are read once at boot need a relaunch; settings read per-request update live. Decide which at design time and label the UI accordingly. Vinnstack labels the Claude-models fields "takes effect on the next Vinnstack launch".
