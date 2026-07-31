---
title: "State machines must catch expected-failure operations or they get stuck forever"
created: 2026-07-11
type: lesson
status: seedling
source: "virtual-avatar session 2026-07-11, static/app.js interruptAndAnswer"
tags: [javascript, error-handling, state-machine, async-await, gotcha]
---

# State machines must catch expected-failure operations or they get stuck forever

When a multi-step async state machine (e.g. presenting -> thinking -> answering -> resuming) calls out to something that has a known, expected failure mode — a transcription API that can legitimately return empty on silence/noise, a network call that can time out — that call needs its own try/catch inside the state machine, not just at the top level. An uncaught rejection from one step doesn't just fail that step: it aborts the entire remaining sequence of `await`s in that function, leaving the state machine stuck in whatever state it was in when the exception fired, with no path back to a working state.

Concrete case (virtual-avatar project, `static/app.js` `interruptAndAnswer()`): an audience-question flow pauses a presentation, records a question, and asks the backend to transcribe + answer it. If nothing intelligible was captured (background noise falsely triggered voice detection, or the person didn't say anything usable), the backend correctly returns 400. But the calling code had no try/catch around that one call — so the thrown error skipped every subsequent step (playing the answer, and critically, RESUMING the paused presentation), leaving the avatar frozen mid-question forever with no visible error and no way to continue except a full page reload.

Fix: wrap specifically the call with a known expected-failure mode in try/catch, show/log a friendly message, and fall through to the state machine's recovery path (here: resume presenting) regardless of whether that one step succeeded. Don't wrap the whole function blindly — target the operation that can legitimately fail in normal operation, so a real bug elsewhere still surfaces loudly.

This is the same root lesson as [[Unguarded top-level await in a module script blocks every statement after it]], recurring in a different function — worth checking every `await` in a stateful control-flow function for "can this legitimately fail, and if so, does the state machine still reach a recoverable state afterward?"

## Related

- [[Unguarded top-level await in a module script blocks every statement after it]]
