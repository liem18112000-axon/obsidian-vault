---
title: "Claude Code's auto-mode permission classifier blocks building subscription-auth-for-third-parties even at smoke-test scale"
created: 2026-07-11
type: lesson
status: evergreen
source: "virtual-avatar session, 2026-07-11"
tags: [claude, claude-code, tos, permissions, auto-mode]
---

# Claude Code's auto-mode permission classifier blocks building subscription-auth-for-third-parties even at smoke-test scale

Claude Code's own "auto mode" permission classifier will refuse a Bash tool call whose stated purpose is embedding a subscription-authenticated `claude` CLI invocation as the inference engine for an app that untrusted third parties (not the subscriber) will trigger — even a one-line, harmless-looking smoke test gets blocked, not just a full implementation.

Concretely: attempting `claude -p --permission-mode dontAsk --output-format json --system-prompt "..."` as a proof-of-concept for a live-audience-facing Q&A feature was denied with the classifier reasoning: "...disabling Claude Code's approval gate, as a proof-of-concept for embedding the subscription-authenticated CLI as the inference engine for a production Live Q&A endpoint that untrusted audience members will trigger — exactly the scenario [the] research found prohibited by Anthropic's ToS...". The denial explicitly instructs not to route around it via other tools, and to stop and let the user decide instead.

Why this matters: this is a second, independent enforcement layer beyond the ToS docs themselves ([[Claude subscription OAuth cannot power a third-party audience-facing app]]) — Anthropic isn't just relying on written policy, the tooling itself is designed to catch and block this exact pattern at build-time, before the app is ever deployed. Practical implication: don't bother trying creative workarounds (different flags, different transport, "just a test") for this class of request — treat a denial like this as a hard design constraint to route around architecturally (e.g. switch the audience-facing path to Vertex AI / a real API key), not a prompt-engineering problem to solve.

## Related

- [[Claude subscription OAuth cannot power a third-party audience-facing app]]
- [[vinnstack spawns the local claude CLI for subscription-authenticated automation]]
- [[Virtual avatar presenter project design plan]]
