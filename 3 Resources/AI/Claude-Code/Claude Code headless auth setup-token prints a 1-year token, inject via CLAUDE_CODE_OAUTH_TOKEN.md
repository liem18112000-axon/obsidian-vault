---
ai_hash: 7239ed69f7754e5b
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-04
entities: []
source: session 2026-07-04, claude-code-guide agent
status: seedling
tags:
- claude-code
- auth
- headless
- ci
title: 'Claude Code headless auth: setup-token prints a 1-year token, inject via CLAUDE_CODE_OAUTH_TOKEN'
type: reference
---

# Claude Code headless auth: setup-token prints a 1-year token, inject via CLAUDE_CODE_OAUTH_TOKEN

Authenticating Claude Code (v2.1.x) on a headless/remote machine where the browser is elsewhere:

- `claude auth login --claudeai` uses a loopback localhost callback by DEFAULT, with a PASTE-BACK fallback ("If your browser shows a login code instead of redirecting... paste it at the Paste code here if prompted prompt. Common in WSL2, SSH, containers."). So it CAN complete without the browser reaching the CLI's machine. Writes credentials into `~/.claude/.credentials.json` (or `$CLAUDE_CONFIG_DIR`).
- `claude setup-token` = purpose-built for CI/headless: prints a URL, user authenticates in any browser, then the terminal PRINTS a one-year OAuth token. It does NOT save the token — the caller captures stdout.
- Inject a pre-obtained token with env `CLAUDE_CODE_OAUTH_TOKEN=<token>` (auth precedence: cloud-provider vars > ANTHROPIC_AUTH_TOKEN > ANTHROPIC_API_KEY > apiKeyHelper > CLAUDE_CODE_OAUTH_TOKEN > interactive subscription OAuth). This lets a token minted anywhere run Claude on the pod with zero interactive login.
- RFC 8628 device-code flow is NOT implemented in v2.1.x (open issue #22992).

Design implication: for per-account CLI homes, EITHER relay `auth login` paste-back (session lands in the account's CLAUDE_CONFIG_DIR) OR relay `setup-token` and store the token per account, injecting CLAUDE_CODE_OAUTH_TOKEN into that account's spawn env (survives restarts, one-year validity, cleaner to capture).

## Related

- [[Relay a headless CLI paste-back OAuth through a web UI with a two-request child registry]]

%% ai-graph-start %%

**Related notes:**
- [[Anthropic has no third-party OAuth; in-app Claude login means driving the claude auth CLI]]
- [[Claude CLI OAuth paste-back expects CODE#STATE, not the bare authorization code]]
- [[Interactive OAuth CLIs need a PTY - wrap in script(1), force wide cols, strip ANSI to parse the URL]]
- [[Prefer pasting a token minted once over scraping it from a PTY relay]]
- [[Relay a headless CLI paste-back OAuth through a web UI with a two-request child registry]]

%% ai-graph-end %%