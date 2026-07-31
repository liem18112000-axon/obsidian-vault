---
title: Designing an Accesstrade skill for Claude Code
created: 2026-06-11
type: howto
status: seedling
source: research session 2026-06-11
tags:
  - claude-code
  - skills
  - accesstrade
  - howto
aliases:
  - Accesstrade skill
  - Build Accesstrade Claude skill
---

# Designing an Accesstrade skill for Claude Code

**Wrap the Accesstrade API once as a single skill whose `SKILL.md` carries the *playbook* and whose bundled script carries the *HTTP/auth plumbing* — then every affiliate task becomes a natural-language request.** Putting the credential and request logic in a script (not the prompt) keeps the token out of context and makes calls deterministic.

## Layout

```text
~/.claude/skills/accesstrade/
├── SKILL.md          # when-to-use + command recipes
├── reference.md      # full endpoint table (loaded on demand)
└── scripts/
    └── at.py         # reads $ACCESSTRADE_KEY, calls the API, prints JSON
```

## SKILL.md sketch

```yaml
---
name: accesstrade
description: >
  Operate the Accesstrade affiliate API: list/join campaigns, mint tracking
  links, pull conversion/earnings reports, fetch datafeeds and coupons.
  Use when the user mentions Accesstrade, affiliate links, commissions, or campaigns.
allowed-tools: Bash(python3 *)
---

# Accesstrade

Use the bundled client; it injects auth from $ACCESSTRADE_KEY.

- Campaigns:  `python3 ${CLAUDE_SKILL_DIR}/scripts/at.py campaigns --running`
- New link:   `python3 ${CLAUDE_SKILL_DIR}/scripts/at.py link --campaign X --url U --sub1 S`
- Earnings:   `python3 ${CLAUDE_SKILL_DIR}/scripts/at.py conversions --since 7d`

For the full endpoint list and params, see [reference.md](reference.md).
Never print the key. Report money as approved vs pending separately.
```

## The script does the dangerous parts

`at.py` owns: reading the key from env, the `Authorization: Token` header, pagination loops, the 5-min/7-day windowing for reports, and JSON output Claude can parse. Claude orchestrates; the script executes. This is the [[Claude Code Skill anatomy|"script does the work, Claude orchestrates"]] pattern.

```mermaid
flowchart TD
    Q[User: 'make links for these 10 products'] --> S[accesstrade skill loads]
    S --> R[Claude calls at.py link ...]
    R -->|env key| API[Accesstrade API]
    API --> R
    R --> O[Claude formats table + posts]
```

## Design choices

- **Read vs write split:** consider `disable-model-invocation: true` on a *separate* write skill (link minting, applying to campaigns) so Claude never takes money-moving actions unprompted; keep read/report auto-invocable.
- **`context: fork`** for heavy report pulls so a 30-day windowed crawl doesn't bloat the main conversation.
- Bundle the endpoint table in `reference.md` so it loads only when Claude needs exact params — keeps the body light.

## Related

- [[Claude Code Skill anatomy]]
- [[Secrets handling for affiliate API keys]]
- [[3 Resources/AI/Claude-Code/Hooks/Claude Code hooks event model]]
- [[Skills vs Hooks vs MCP vs subagents]]
- [[Accesstrade API Integration - MOC]]
