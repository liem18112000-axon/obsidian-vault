---
title: "Module-level path consts freeze a runtime-configurable dir at import time"
created: 2026-07-27
type: lesson
status: seedling
source: "vinnstack onboarding dataRoot test, session 2026-07-27"
tags: [nodejs, config, gotcha, module-cache, vinnstack]
---

# Module-level path consts freeze a runtime-configurable dir at import time

A value that the user can reconfigure at runtime (here: the Vinnstack data folder / note-vault path, chosen in onboarding) must be resolved PER CALL, not captured once in a module-level `const` at import time. If a store module does `export const INTERROGATION_DIR = path.join(getConfig().vaultPath, "...")` at top level, that path is frozen to whatever `getConfig()` returned the first time the module was imported.

Failure mode (vinnstack, 2026-07): the Next server boots and imports the store modules BEFORE onboarding runs (so vaultPath = home default). Onboarding then saves the chosen dataRoot and calls only `window.location.reload()` — which reloads the renderer, NOT the server process. Node`s module cache keeps the already-imported modules, so `INTERROGATION_DIR` / `CONTENT_DIR` stay pinned to the home default. The operator`s first notes/interrogations land in the OLD folder until a full app restart. Meanwhile `getConfig()` itself is fine — it re-reads config each call; only the captured consts are stale.

Fix: replace the import-time consts with functions (`interrogationDir()`, `contentDir()`) that call `getConfig()` each time. Verify with a test that imports the store in an ERA1 env, changes the dir env WITHOUT re-importing, and asserts the store now points at ERA2.

Related: [[Guard array-typed React state seeded from a fetch with ?? []]] (both are "the value went stale/undefined" defects).

## Related

- [[Guard array-typed React state seeded from a fetch with ?? []]]
