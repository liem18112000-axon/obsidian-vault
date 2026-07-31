---
title: "Vinnstack AI calls are stateless headless claude CLI runs, not an agent runtime"
created: 2026-07-12
type: concept
status: seedling
source: "session 2026-07-12 — writing doc/vinnstack-bdd-pipeline.html"
tags: [vinnstack, architecture, claude-code, cli]
---

# Vinnstack AI calls are stateless headless claude CLI runs, not an agent runtime

Vinnstack (a locally-hosted Next.js app for AI-driven dev workflows) does NOT run a persistent multi-agent framework or use the Anthropic Messages API/SDK directly for its Interrogation Room or BDD pipelines. Every AI call goes through lib/ai/claudeRun.ts::runClaude(), which spawns the operator's own `claude` CLI headlessly (`claude -p --output-format json --permission-mode dontAsk ...`), with the prompt piped via stdin (avoids Windows' ~32K argv length cap) and auth relying entirely on the operator's Claude Code OAuth login (no API key — a deliberate choice; `--bare` would force key-only auth).

Each "skill" is a real vinnstack-skills/<name>/SKILL.md file read fresh off disk on every call (lib/ai/skillPrompt.ts::loadSkill/compose) — the skill body itself IS the system prompt, with a small code-owned "OUTPUT CONTRACT" block appended per call site to pin the exact JSON/Markdown shape the TypeScript parser expects. This means the shipped SKILL.md and what the model is actually told never drift apart — there is no separate prompt-template copy to keep in sync.

Net effect: the architecture is plain CLI orchestration (stateless subprocess calls driven by ordinary API routes/store functions), not an agent SDK or long-running agent process. Useful mental model when reasoning about cost, concurrency, or debugging a "why did the AI do X" question in this codebase — the answer is always "read the SKILL.md + the OUTPUT CONTRACT that was actually composed for that call."

## Related
[[Vinnstack ai-framework.html is aspirational, not the real code]]
[[3 Resources/Work-Side/Vinnstack/Vinnstack withholds gitgh from the model in BDD step implementation]]
