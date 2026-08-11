---
ai_hash: 358edb4605eb4a6e
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-04
entities: []
source: session 2026-07-04, vinnstack claude setup-token relay
status: seedling
tags:
- pty
- subprocess
- relay
- vinnstack
- timeout
title: Harvest CLI output on stream-match, not on process close, when the CLI lingers
  after printing
type: gotcha
---

# Harvest CLI output on stream-match, not on process close, when the CLI lingers after printing

When you drive an interactive CLI through a PTY and need a value it prints (a token, a URL, a code), harvest it by MATCHING THE OUTPUT STREAM as data arrives — do not wait for the child process to close and only then scan the buffer. Some CLIs print the value and then STAY ALIVE: `claude setup-token` prints the sk-ant-oat token, then holds a success screen open, and a `script -qfec` PTY wrapper keeps the pipe open regardless. So a close-gated harvester never fires; it just burns the full timeout and returns failure while the token sits unharvested in the output buffer the whole time.

Tell: the finish step failing after an EXACT round-number wall-clock (e.g. 120000ms) is the signature of "waited for an event that never came, then timed out" — not a slow operation.

Fix pattern: attach a data listener that re-tests the capture regex on every chunk; settle success the instant it matches, then kill the lingering child to reap it. Keep the close handler as a fallback for CLIs that DO exit cleanly (e.g. `gcloud auth login --no-launch-browser`), and run one capture check immediately after wiring the listener in case the value already streamed in before you attached. Contrast: waiting-for-URL at START already did this correctly; the SUBMIT step regressed to close-gated. Match-based capture is the rule for both ends of a relay.

## Related

- [[Seed-in-memory-but-persist-on-save leaves no row when a prior layer already shows connected]]

%% ai-graph-start %%

**Related notes:**
- [[Driving a raw-mode or ink TTY prompt through a PTY needs carriage return, not newline, to submit]]
- [[Prefer pasting a token minted once over scraping it from a PTY relay]]
- [[Interactive OAuth CLIs need a PTY - wrap in script(1), force wide cols, strip ANSI to parse the URL]]
- [[Relay a headless CLI paste-back OAuth through a web UI with a two-request child registry]]
- [[Claude CLI OAuth paste-back expects CODE#STATE, not the bare authorization code]]

%% ai-graph-end %%