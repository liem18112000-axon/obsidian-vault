---
ai_hash: 355d7e448ad9264e
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
aliases:
- Affiliate API key security
- Storing Accesstrade token
created: 2026-06-11
entities: []
source: research session 2026-06-11
status: seedling
tags:
- security
- secrets
- accesstrade
- claude-code
- lesson
title: Secrets handling for affiliate API keys
type: lesson
---

# Secrets handling for affiliate API keys

**The Accesstrade access key is a long-lived bearer of your entire account — treat it like a password: it lives in an environment variable or secret store, never in a note, prompt, SKILL.md, repo, or canvas.** Because the key doesn't auto-expire, a single leak is durable until you manually rotate it.

## Do

- Store it in an **environment variable** (`ACCESSTRADE_KEY`) or an OS secret manager; have the [[Designing an Accesstrade skill for Claude Code|skill's script]] read it at runtime.
- For `http` hooks, pass secrets via `allowedEnvVars` + a header, not inline in `settings.json`.
- Keep `.env` files out of git (`.gitignore`) and out of the vault.
- **Rotate** immediately if exposed (regenerate in the dashboard) — see [[Accesstrade Publisher API authentication]].

## Don't

- Don't paste the key into a chat to "test quickly" — prompts get logged and may be captured by knowledge hooks.
- Don't commit it to a project skill that lives in version control.
- Don't write it into these notes. If you must record *that* a key exists, record only a non-sensitive label like `ACCESSTRADE_KEY (env)` or `<redacted>`.

## Why this note exists

This vault runs an **aggressive knowledge-capture directive** and several messaging hooks. That makes it doubly important: a secret typed into context could be auto-filed into a note or forwarded to Slack/Telegram. The obsidian-note skill's own rule is explicit — *never store secrets; redact*. Keep that discipline for every affiliate credential.

> [!danger] One-line rule
> If a value would let someone else spend or read your affiliate account, it never appears as plaintext in the vault.

## Related

- [[Accesstrade Publisher API authentication]]
- [[Designing an Accesstrade skill for Claude Code]]
- [[3 Resources/AI/Claude-Code/Hooks/Claude Code hooks event model]]
- [[Accesstrade API Integration - MOC]]

%% ai-graph-start %%

**Related notes:**
- [[Designing an Accesstrade skill for Claude Code]]
- [[Accesstrade Publisher API authentication]]
- [[Accesstrade API Integration - MOC]]
- [[Affiliate API engineering best practices]]
- [[Claude Code Skill anatomy]]

%% ai-graph-end %%