---
title: "When a merge turns CI red decide test-vs-source fix by reading code intent"
created: 2026-07-26
type: lesson
status: seedling
source: "vinnstack cloud-build fix, session 2026-07-26"
tags: [testing, ci, debugging, decision]
---

# When a merge turns CI red decide test-vs-source fix by reading code intent

When a feature merge turns CI red with many failing tests, the first decision per failure is: fix the TEST or fix the SOURCE? Heuristic — read the code and its comments for INTENT.

Most post-merge failures are stale tests trailing behavior the merge changed on purpose. Signals it is the test that is behind:
- The source has explanatory comments describing the new behavior (e.g. "auto-open the rail even with zero comments so the gesture is discoverable").
- The API/response shape gained fields the fixture doesn`t provide (add them to the fixture / expected body).
- A UI change multiplied elements so a singular query breaks — `getByRole("combobox")` throws "multiple elements" once per-stage `<select>`s were added; switch to `getAllByRole(...)[0]` targeting the intended one.
- Behavior became conditional (Electron GPU now decided by `decideGpu()` instead of an unconditional `app.disableHardwareAcceleration()`); rewrite the test to assert each branch.

Only a GENUINE robustness gap gets a source fix — e.g. an unguarded `setState(j.list)` that crashes on a sparse response (see [[Guard array-typed React state seeded from a fetch with ?? []]]). Tell them apart by asking whether the new behavior was intended (comments, the feature`s purpose) versus an accidental crash.

Reproduce locally and re-run rather than trusting the CI log alone — see [[A render crash masks latent crashes elsewhere in the same React subtree]].

## Related

- [[Guard array-typed React state seeded from a fetch with ?? []]]
- [[A render crash masks latent crashes elsewhere in the same React subtree]]
