---
ai_hash: 611745667cda12f9
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-06
entities: []
source: vinnstack dev relay 2026-07-06, claude setup-token
status: seedling
tags:
- oauth
- claude-cli
- setup-token
- relay
- vinnstack
title: Claude CLI OAuth paste-back expects CODE#STATE, not the bare authorization
  code
type: gotcha
---

# Claude CLI OAuth paste-back expects CODE#STATE, not the bare authorization code

`claude setup-token` (and the manual paste-back path of `claude login`) print an auth URL, and after you authorize the browser lands on `https://platform.claude.com/oauth/code/callback?code=<CODE>&state=<STATE>`. The value the CLI's "Paste code here" prompt wants is NOT the bare `code` — it is `CODE#STATE`: the authorization code and the state parameter joined with a literal `#`. Pasting only the code, or the whole URL, leaves the exchange unable to complete: setup-token accepts the input (echoes it masked) but never prints the `sk-ant-oat…` token, so an automating harness just hangs to its timeout with no token.

State is joined because the flow is PKCE + CSRF: the CLI needs the state it generated to validate the callback and complete the token exchange.

When automating this (e.g. a web relay that feeds the paste value to setup-token over a PTY), normalize whatever the operator pastes — full callback URL, bare code, or already-formatted CODE#STATE — into CODE#STATE before sending: if it already contains '#' pass through; else regex out code= and state= from the URL/query and join them. Pair with sending Enter as \r (see linked note) — both are required for the paste-back to actually complete.

## Related

- [[3 Resources/Backend/CLI/Driving a raw-mode or ink TTY prompt through a PTY needs carriage return, not newline, to submit]]

%% ai-graph-start %%

**Related notes:**
- [[Interactive OAuth CLIs need a PTY - wrap in script(1), force wide cols, strip ANSI to parse the URL]]
- [[Prefer pasting a token minted once over scraping it from a PTY relay]]
- [[Claude Code headless auth setup-token prints a 1-year token, inject via CLAUDE_CODE_OAUTH_TOKEN]]
- [[Driving a raw-mode or ink TTY prompt through a PTY needs carriage return, not newline, to submit]]
- [[Anthropic has no third-party OAuth; in-app Claude login means driving the claude auth CLI]]

%% ai-graph-end %%