---
ai_hash: 8ad43da0807ad19b
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-06
entities: []
source: vinnstack dev relay 2026-07-06, claude setup-token
status: seedling
tags:
- pty
- tty
- ink
- relay
- vinnstack
- enter
title: Driving a raw-mode or ink TTY prompt through a PTY needs carriage return, not
  newline, to submit
type: gotcha
---

# Driving a raw-mode or ink TTY prompt through a PTY needs carriage return, not newline, to submit

When you automate a modern CLI prompt (ink/React-based, or anything using raw-mode readline — e.g. `claude setup-token`) by writing to its PTY stdin, terminate the input with a CARRIAGE RETURN `\r` (0x0D), not a line feed `\n` (0x0A). A real terminal sends `\r` when you press Enter; a raw-mode reader parses keypress events and only treats `\r` as the Enter/submit key. A bare `\n` is seen as a different (often ignored) key, so the pasted value is typed but never SUBMITTED — the CLI sits at the prompt forever.

`\r` is strictly safer than `\n` for PTY input: in canonical/cooked mode the tty's ICRNL setting translates incoming `\r` → `\n` and completes the line anyway, while in raw mode `\r` is the Enter key. So send `\r` in both cases.

Symptom that fingerprints this: the automated flow reaches the prompt fine (e.g. the auth URL is captured), you write the code, and then the step fails after an EXACT round-number timeout (120000ms) with nothing produced — because the tool is still waiting for Enter. Same exact-timeout tell as a close-gated harvester, but the cause is unsubmitted input, not unharvested output. When unsure which, log a redacted tail of the accumulated PTY output on timeout to see whether the tool emitted anything at all.

## Related

- [[3 Resources/Backend/CLI/Harvest CLI output on stream-match, not on process close, when the CLI lingers after printing]]

%% ai-graph-start %%

**Related notes:**
- [[Harvest CLI output on stream-match, not on process close, when the CLI lingers after printing]]
- [[Interactive OAuth CLIs need a PTY - wrap in script(1), force wide cols, strip ANSI to parse the URL]]
- [[Prefer pasting a token minted once over scraping it from a PTY relay]]
- [[Claude CLI OAuth paste-back expects CODE#STATE, not the bare authorization code]]
- [[Spawning a prompting CLI hangs on open stdin — use stdio stdin ignore for EOF]]

%% ai-graph-end %%