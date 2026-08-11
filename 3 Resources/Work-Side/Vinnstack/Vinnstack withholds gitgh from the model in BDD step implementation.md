---
ai_hash: 62ce860c76b6a965
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-12
entities: []
source: session 2026-07-12 — writing doc/vinnstack-bdd-pipeline.html
status: seedling
tags:
- vinnstack
- architecture
- ai-safety
- design-pattern
title: Vinnstack withholds git/gh from the model in BDD step implementation
type: lesson
---

# Vinnstack withholds git/gh from the model in BDD step implementation

In Vinnstack's BDD test-implementation pipeline (lib/bdd/implementRunner.ts, using the implement-bdd-steps skill), the headless Claude run that writes Python/behave step definitions is invoked with `Bash(git*)` and `Bash(gh*)` explicitly disallowed. Vinnstack's own TypeScript code (lib/bdd/implementGit.ts) owns every git/PR operation instead — creating the worktree, committing `features/**` only, pushing, and opening/reusing the Bitbucket PR.

This is a deliberate architecture boundary: the model is trusted to author and verify code (it must actually run `behave` and report a real pass/fail), but never to decide what gets committed, pushed, or how a PR gets shaped — that stays in deterministic, reviewable application code. Worth remembering as a reusable pattern for "AI writes/verifies, app code ships" whenever designing a similar AI-driven pipeline: keep the risky, hard-to-review side effects (git history, PR creation) out of the model's tool access even when the model is otherwise free to edit files and run tests.

## Related
[[Vinnstack AI calls are stateless headless claude CLI runs, not an agent runtime]]

%% ai-graph-start %%

**Related notes:**
- [[Vinnstack AI calls are stateless headless claude CLI runs, not an agent runtime]]
- [[vinnstack BDD pipeline stops at JiraXray, never writes files into a cloned repo]]
- [[Vinnstack ai-framework.html is aspirational, not the real code]]
- [[luz_docs_integration_test has its own AI-driven BDD pipeline (generate, implement, PR agents)]]
- [[Vinnstack vinnstack-data-model.html predates the BDD workspace]]

%% ai-graph-end %%