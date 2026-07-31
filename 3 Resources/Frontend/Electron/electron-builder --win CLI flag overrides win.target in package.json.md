---
title: "electron-builder --win CLI flag overrides win.target in package.json"
created: 2026-07-15
type: lesson
status: seedling
source: "Vinnstack session 2026-07-15"
tags: [electron-builder, electron, ci, gotcha, vinnstack]
---

# electron-builder --win CLI flag overrides win.target in package.json

electron-builder's `--win <target>` CLI flag (e.g. `--win portable`) **overrides** the `win.target` declared in package.json's `build` block. Editing `win.target` alone is therefore *not* authoritative — if any invocation site still passes an explicit `--win <target>`, that flag wins and the config change silently does nothing.

**Symptom:** electron-builder logs `building target=portable` even though package.json says `"target": "nsis"`, and a downstream step that expects the config's artifact name (e.g. uploading `dist/vinnstack-setup.exe`) fails with `matched no objects` because the portable build produced a differently-named file (`dist/Vinnstack 1.0.0.exe`).

**Fix:** grep **every** invocation site for a hard-coded target — npm scripts in package.json (`dist:win`) *and* CI steps (cloudbuild.yaml `package-exe`) — and change them all. To defer entirely to the config, pass just `--win` with no target.

Context: Vinnstack Electron app. Both `dist:win` and the Cloud Build `package-exe` step ran `electron-builder --win portable`, which overrode a package.json switch to `nsis` and cost a full build cycle before the flag was noticed.

## Related

- [[Electron]]
