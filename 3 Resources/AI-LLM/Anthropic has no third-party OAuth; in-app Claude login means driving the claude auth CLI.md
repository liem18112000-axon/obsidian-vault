---
title: "Anthropic has no third-party OAuth; in-app Claude login means driving the claude auth CLI"
created: 2026-07-01
type: lesson
status: seedling
source: "session 2026-07-01 (Vinnstack provider auth layer)"
tags: [claude, anthropic, oauth, auth, cli, gcloud, vinnstack]
---

# Anthropic has no third-party OAuth; in-app Claude login means driving the claude auth CLI

**Feasibility finding (had to dig):** There is NO third-party OAuth authorization-code flow where your app registers as an OAuth client and mints Anthropic API tokens. Anthropic API access is API-key based, OR via the first-party login that Claude Code / the `ant` CLI perform. That first-party login IS a real browser OAuth flow, but only the vendor's own CLI can drive it.

**So "add OAuth login for Claude" in a local app = drive the Claude Code CLI's own login**, not implement an OAuth client:
- `claude auth login [--claudeai|--console|--sso] [--email X]` — opens the browser, subscription OAuth by default; process exits when sign-in completes.
- `claude auth logout`
- `claude auth status` — emits clean JSON: `{loggedIn, authMethod, apiProvider, email, orgId, orgName, subscriptionType}`. Parse this for a status UI (no need to read the credential file).
- `claude setup-token` mints a long-lived token (subscription required).
- Credential lives at `~/.claude/.credentials.json` (Windows too); `ant` uses `%APPDATA%\Anthropic`.

**Google Cloud is the same shape:** `gcloud auth login` / `gcloud auth application-default login` (browser), `gcloud auth list --filter=status:ACTIVE --format=value(account)` for status, ADC file at `%APPDATA%\gcloud\application_default_credentials.json`. Gotcha: on Windows `gcloud` is a .cmd wrapper — Node `spawn` without `shell:true` can't run it (ENOENT); `claude` resolves to a real .exe so it doesn't need a shell.

**Design pattern that worked (Vinnstack):** an `AuthProvider` interface (id, kind, status(), login(signal), logout()) with one impl per vendor + a registry; `/api/auth` (list+status) and `/api/auth/[provider]` (POST login|logout). login() spawns the CLI browser flow with a long, abortable timeout; status() is a quick bounded exec. This is how you 'separate the layer' from Claude while supporting only Claude initially.
