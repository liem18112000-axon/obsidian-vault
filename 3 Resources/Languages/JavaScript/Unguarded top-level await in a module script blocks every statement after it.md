---
title: "Unguarded top-level await in a module script blocks every statement after it"
created: 2026-07-11
type: lesson
status: seedling
source: "virtual-avatar session 2026-07-11, static/app.js"
tags: [javascript, async-await, es-modules, error-handling, gotcha]
---

# Unguarded top-level await in a module script blocks every statement after it

In a JS `<script type="module">`, top-level `await` statements run sequentially, and if one throws (or never resolves) without a try/catch, every statement written after it in that module simply never executes — there's no visible crash, just silent non-execution of the rest of the script.

Concrete case (virtual-avatar project, `static/app.js`): the script ended with three sequential top-level awaits:
```js
await head.showAvatar({ url: AVATAR_URL });  // NOT wrapped in try/catch
await loadSections();                          // populates slide buttons
await loadModels();                             // populates the avatar-picker <select>
```
If `showAvatar()` failed or hung (e.g., a slow/broken default avatar file), `loadSections()` and `loadModels()` never ran — leaving the UI stuck showing its initial static placeholder text (`"Loading models…"` in the `<select>`) forever, with no error surfaced anywhere. The actual failure was in the first line, but the *symptom* showed up as "the second and third features look broken/stuck."

Fix: wrap the risky operation in the same try/catch used elsewhere for the same operation (this codebase already had a `switchAvatar()` helper with try/catch for later avatar switches — reusing it for the *initial* load too, instead of a bare unguarded call, fixed it) so a failure there can't cascade into blocking every subsequent independent initialization step.

General lesson: when a module's initialization does several independent things via sequential top-level `await`, guard each one (or at least the risky/network-dependent ones) individually — an unguarded early step can silently prevent unrelated later steps from ever running, and the resulting bug report will describe the *later* steps as broken, not the actual failing one.
