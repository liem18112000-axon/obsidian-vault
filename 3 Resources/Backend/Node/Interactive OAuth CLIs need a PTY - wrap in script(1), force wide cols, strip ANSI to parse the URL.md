---
ai_hash: fa388916f8844cfe
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-04
entities: []
source: session 2026-07-04, vinnstack claude login relay
status: seedling
tags:
- pty
- cli
- oauth
- ansi
- claude-code
- vinnstack
title: Interactive OAuth CLIs need a PTY - wrap in script(1), force wide cols, strip
  ANSI to parse the URL
type: gotcha
---

# Interactive OAuth CLIs need a PTY - wrap in script(1), force wide cols, strip ANSI to parse the URL

`claude setup-token` (and modern interactive CLIs generally) emit NOTHING to piped stdio — they gate their prompt UI on an interactive terminal. `spawn(cmd, {stdio:['pipe','pipe','pipe']})` → 0 bytes captured, even to a file. To drive such a CLI from a server, give it a pseudo-terminal:
- Zero-dep: `script -qfec "<cmdline>" /dev/null` (util-linux `script`, present in most images) runs the command in a PTY while relaying stdio. node-pty works too but is a native build dep.
- The PTY renders at 80 cols by default and WRAPS long output — a paste-back OAuth URL gets broken across ~4 lines with ANSI escapes injected mid-string, so a naive `/https?:\/\/\S+/` grabs a truncated URL. Force a wide terminal first: `script -qfec "stty cols 4000; <cmd>" /dev/null`.
- Output is full of ANSI/escape sequences (spinners, cursor moves, bracketed-paste `?2004h`). Strip with `s.replace(/\x1B\[[0-9;?]*[A-Za-z]/g,'')` before regex-matching the URL or detecting the "paste code" prompt.

Bonus finding: claude setup-token's OAuth uses `redirect_uri=https://platform.claude.com/oauth/code/callback&code=true` — a GLOBAL callback that shows the user a code to paste back, NOT a localhost loopback. That's what makes a remote web relay possible at all.

## Related

- [[Relay a headless CLI paste-back OAuth through a web UI with a two-request child registry]]

%% ai-graph-start %%

**Related notes:**
- [[Claude CLI OAuth paste-back expects CODE#STATE, not the bare authorization code]]
- [[Relay a headless CLI paste-back OAuth through a web UI with a two-request child registry]]
- [[Prefer pasting a token minted once over scraping it from a PTY relay]]
- [[Claude Code headless auth setup-token prints a 1-year token, inject via CLAUDE_CODE_OAUTH_TOKEN]]
- [[Driving a raw-mode or ink TTY prompt through a PTY needs carriage return, not newline, to submit]]

%% ai-graph-end %%