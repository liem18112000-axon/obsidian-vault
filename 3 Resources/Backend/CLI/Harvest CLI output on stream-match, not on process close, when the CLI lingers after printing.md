---
title: "Harvest CLI output on stream-match, not on process close, when the CLI lingers after printing"
created: 2026-07-04
type: gotcha
status: seedling
source: "session 2026-07-04, vinnstack claude setup-token relay"
tags: [pty, subprocess, relay, vinnstack, timeout]
---

# Harvest CLI output on stream-match, not on process close, when the CLI lingers after printing

When you drive an interactive CLI through a PTY and need a value it prints (a token, a URL, a code), harvest it by MATCHING THE OUTPUT STREAM as data arrives — do not wait for the child process to close and only then scan the buffer. Some CLIs print the value and then STAY ALIVE: `claude setup-token` prints the sk-ant-oat token, then holds a success screen open, and a `script -qfec` PTY wrapper keeps the pipe open regardless. So a close-gated harvester never fires; it just burns the full timeout and returns failure while the token sits unharvested in the output buffer the whole time.

Tell: the finish step failing after an EXACT round-number wall-clock (e.g. 120000ms) is the signature of "waited for an event that never came, then timed out" — not a slow operation.

Fix pattern: attach a data listener that re-tests the capture regex on every chunk; settle success the instant it matches, then kill the lingering child to reap it. Keep the close handler as a fallback for CLIs that DO exit cleanly (e.g. `gcloud auth login --no-launch-browser`), and run one capture check immediately after wiring the listener in case the value already streamed in before you attached. Contrast: waiting-for-URL at START already did this correctly; the SUBMIT step regressed to close-gated. Match-based capture is the rule for both ends of a relay.

## Related

- [[Seed-in-memory-but-persist-on-save leaves no row when a prior layer already shows connected]]
