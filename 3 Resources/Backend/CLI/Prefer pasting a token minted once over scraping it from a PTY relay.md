---
ai_hash: 0bfa018ea689890d
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-06
entities: []
source: vinnstack dev claude auth 2026-07-06, 401 Invalid bearer token
status: seedling
tags:
- oauth
- token-capture
- relay
- claude-cli
- vinnstack
title: Prefer pasting a token minted once over scraping it from a PTY relay
type: decision
---

# Prefer pasting a token minted once over scraping it from a PTY relay

Capturing a secret token by regex-scraping a CLI's PTY output (e.g. relaying `claude setup-token` through `script`, then matching /sk-ant-.../ on the ANSI-stripped stream) is fragile in two compounding ways: (1) the captured bytes can be truncated/mangled by wrapping, ANSI interleaving, or regex boundaries — yielding a token that stores fine but fails auth with 401 "Invalid bearer token"; and (2) re-running the mint command (common while debugging the relay) can revoke the previously-captured token, so the stored one goes stale. Symptom: the CLI runs and reaches the API (isError:true, one turn, ~59-char result "Failed to authenticate. API Error: 401 Invalid bearer token"), not a spawn/connect failure.

Robust alternative: let the human mint the token ONCE in a real terminal where the flow works natively, then PASTE the value into a field; store it verbatim. No PTY, no scraping, no truncation, no supersede-race. For a headless server that just needs the CLI authenticated, "paste a known-good token" beats "relay the interactive login" every time. (Or sidestep the subscription token entirely with a cloud backend that auths via ADC, e.g. Claude-on-Vertex.)

## Related

- [[Claude CLI OAuth paste-back expects CODE#STATE]]
- [[not the bare authorization code]]

%% ai-graph-start %%

**Related notes:**
- [[Claude CLI OAuth paste-back expects CODE#STATE, not the bare authorization code]]
- [[Interactive OAuth CLIs need a PTY - wrap in script(1), force wide cols, strip ANSI to parse the URL]]
- [[Harvest CLI output on stream-match, not on process close, when the CLI lingers after printing]]
- [[Claude Code headless auth setup-token prints a 1-year token, inject via CLAUDE_CODE_OAUTH_TOKEN]]
- [[Driving a raw-mode or ink TTY prompt through a PTY needs carriage return, not newline, to submit]]

%% ai-graph-end %%