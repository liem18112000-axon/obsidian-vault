---
title: "Config read into a module-level const applies only on next process launch"
created: 2026-07-02
type: gotcha
status: seedling
tags: [nodejs, config, module-load, module-cache, nextjs, gotcha, vinnstack]
entities: [getConfig, reloadConfig, "window.location.reload()", sys.modules-equivalent, CLAUDE_MODEL, INTERROGATION_DIR, CONTENT_DIR]
---

# Config read into a module-level const applies only on next process launch

Any runtime-reconfigurable value captured into a module-level `const` at import time is frozen for the life of that module instance:

```js
export const CLAUDE_MODEL = getConfig().anthropicModel;                 // frozen
export const INTERROGATION_DIR = path.join(getConfig().vaultPath, "…"); // frozen
```

Persisting a new value at runtime updates the config file and `reloadConfig()`'s cache, but the already-evaluated const does not change — Node's module cache keeps the imported module, so the running process keeps the old value. `getConfig()` itself is fine; only the captured consts go stale.

**Fix — resolve per call.** Replace import-time consts with functions (`interrogationDir()`, `contentDir()`) that call `getConfig()` each time. Verify with a test that imports the store under one env value, changes the env **without re-importing**, and asserts the module now points at the new value.

**Rule of thumb:** settings read once at boot need a relaunch; settings read per request update live. Decide which at design time and label the UI accordingly (Vinnstack labels the Claude-model fields "takes effect on the next Vinnstack launch").

**Sharpest failure mode (Vinnstack 2026-07):** the Next server imports the store modules *before* onboarding runs, so `vaultPath` is the home default. Onboarding saves the chosen dataRoot and calls only `window.location.reload()` — which reloads the renderer, **not** the server process. The operator's first notes land in the OLD folder until a full app restart.

## Related

- [[Guard array-typed React state seeded from a fetch with ?? []]]
- [[Enable Claude Code fast mode in headless -p runs via --settings fastMode]]
