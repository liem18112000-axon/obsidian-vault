---
title: "Hard-exclude an AI agent from a resource by shrinking its file grant, not by prompting"
created: 2026-07-19
type: lesson
status: seedling
source: "Vinnstack session 2026-07-19"
tags: [ai-agents, security, claude-cli, add-dir, vinnstack]
---

# Hard-exclude an AI agent from a resource by shrinking its file grant, not by prompting

To make an AI agent HARD-unable to read a resource (not just instructed to ignore it), remove it from the filesystem tree the agent is granted — don't rely on a prompt.

Vinnstack (2026-07): the chat agent gets the graphify graphs via the Claude CLI --add-dir GRAPHIFY_DIR (~/.agentic-os/graphify). "Disable a repo so the AI won't scan it" is enforced by MOVING that repo's built graph (out/<slug>) OUT of GRAPHIFY_DIR into a sibling dir (~/.agentic-os/graphify-disabled/<slug>) that is NOT on any --add-dir. Enabling moves it back. The AI literally has no path to a disabled repo's graph — a system-prompt "please ignore" would be soft (the agent still has Read/Grep/Glob over the whole --add-dir).

General rule: for tool-having agents, capability = what's inside the granted dirs/tools. Enforce exclusion by shrinking the grant (move/remove files, drop the dir from --add-dir), not by asking the model. DB flags/UI state can track "enabled", but the on-disk move is the actual control.

Related: the per-scan security controls (code-only staging, no-LLM, no-egress, source-deleted-after-scan) are independent of WHICH repo, so a user-managed repo registry keeps them intact even though the old hardcoded allowlist is gone.
