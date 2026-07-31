---
ai_hash: a4fddac2f298db74
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-19
entities:
- AI agent
- resource
- file grant
- prompt
- filesystem tree
- Vinnstack
- chat agent
- graphify graphs
- Claude CLI
- GRAPHIFY_DIR
- repo
- built graph
- out/<slug>
- ~/.agentic-os/graphify-disabled/<slug>
- --add-dir
- system-prompt
- Read/Grep/Glob
- tool-having agents
- capability
- granted dirs
- tools
- DB flags
- UI state
- on-disk move
- per-scan security controls
- code-only staging
- no-LLM
- no-egress
- source-deleted-after-scan
- user-managed repo registry
- hardcoded allowlist
source: Vinnstack session 2026-07-19
status: seedling
tags:
- ai-agents
- security
- claude-cli
- add-dir
- vinnstack
title: Hard-exclude an AI agent from a resource by shrinking its file grant, not by
  prompting
type: lesson
---

# Hard-exclude an AI agent from a resource by shrinking its file grant, not by prompting

To make an AI agent HARD-unable to read a resource (not just instructed to ignore it), remove it from the filesystem tree the agent is granted — don't rely on a prompt.

Vinnstack (2026-07): the chat agent gets the graphify graphs via the Claude CLI --add-dir GRAPHIFY_DIR (~/.agentic-os/graphify). "Disable a repo so the AI won't scan it" is enforced by MOVING that repo's built graph (out/<slug>) OUT of GRAPHIFY_DIR into a sibling dir (~/.agentic-os/graphify-disabled/<slug>) that is NOT on any --add-dir. Enabling moves it back. The AI literally has no path to a disabled repo's graph — a system-prompt "please ignore" would be soft (the agent still has Read/Grep/Glob over the whole --add-dir).

General rule: for tool-having agents, capability = what's inside the granted dirs/tools. Enforce exclusion by shrinking the grant (move/remove files, drop the dir from --add-dir), not by asking the model. DB flags/UI state can track "enabled", but the on-disk move is the actual control.

Related: the per-scan security controls (code-only staging, no-LLM, no-egress, source-deleted-after-scan) are independent of WHICH repo, so a user-managed repo registry keeps them intact even though the old hardcoded allowlist is gone.

%% ai-graph-start %%

**Related notes:**
- [[Unrestricted directory mounts for LLM tools risk multi-megabyte single-tool-call reads]]
- [[A hardcoded allowlist becomes a security boundary, not just data, once it gates credentialed access]]
- [[Vinnstack withholds gitgh from the model in BDD step implementation]]
- [[Headless claude -p loads the user global CLAUDE.md; isolate per-selection AI transforms with delimiters + data-guard]]
- [[Vinnstack AI calls are stateless headless claude CLI runs, not an agent runtime]]

**Relations:**
- AI agent — *is excluded from* — resource
- exclusion — *by* — shrinking file grant
- exclusion — *not by* — prompting
- AI agent — *is unable to read* — resource
- resource — *removed from* — filesystem tree
- filesystem tree — *granted to* — AI agent
- Vinnstack — *describes* — chat agent
- chat agent — *gets* — graphify graphs
- graphify graphs — *via* — Claude CLI
- Claude CLI — *uses* — --add-dir
- GRAPHIFY_DIR — *is* — ~/.agentic-os/graphify
- repo — *has* — built graph
- built graph — *is in* — out/<slug>
- repo — *disabled by moving* — built graph
- built graph — *moved out of* — GRAPHIFY_DIR
- built graph — *moved into* — ~/.agentic-os/graphify-disabled/<slug>
- ~/.agentic-os/graphify-disabled/<slug> — *is not on* — --add-dir
- repo — *enabled by moving* — built graph
- AI — *has no path to* — disabled repo's graph
- system-prompt — *is* — soft
- agent — *has* — Read/Grep/Glob
- Read/Grep/Glob — *over* — --add-dir
- capability — *equals* — what's inside granted dirs/tools
- exclusion — *enforced by* — shrinking grant
- shrinking grant — *involves* — move/remove files
- shrinking grant — *involves* — drop dir from --add-dir
- DB flags — *can track* — enabled
- UI state — *can track* — enabled
- on-disk move — *is* — actual control
- per-scan security controls — *include* — code-only staging
- per-scan security controls — *include* — no-LLM
- per-scan security controls — *include* — no-egress
- per-scan security controls — *include* — source-deleted-after-scan
- per-scan security controls — *are independent of* — WHICH repo
- user-managed repo registry — *keeps intact* — per-scan security controls
- hardcoded allowlist — *is* — gone

%% ai-graph-end %%