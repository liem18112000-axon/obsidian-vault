---
title: "Vinnstack ai-framework.html is aspirational, not the real code"
created: 2026-07-12
type: lesson
status: seedling
source: "session 2026-07-12 — writing doc/vinnstack-bdd-pipeline.html"
tags: [vinnstack, docs, architecture, gotcha]
---

# Vinnstack ai-framework.html is aspirational, not the real code

doc/ai-framework.html in the Vinnstack repo (c:\Users\dvtliem\Kepler\vinnstack) is a generic, standalone pitch/explainer for a conceptual "Agentic Development Lifecycle" — it never mentions Vinnstack, Postgres, or the BDD workspace by name. It names conceptual agents (Orchestrator, RequirementMemory, RepoExplorer, CodeAnalyzer, TestGenerator, StepImplementer, PRCreator, MemoryQuery) that do NOT exist anywhere in the real code (lib/, vinnstack-skills/, app/).

The real BDD/test-generation pipeline is implemented under lib/bdd/ and lib/interrogation/, using a completely different and more specific set of real skills (story-to-bdd-scenarios, implement-bdd-steps, interrogate-qa, story-to-process-flow, critique-artifact, etc.) and plain TypeScript functions — not a persistent multi-agent runtime.

Treat ai-framework.html as vision/pitch material only ("why we build this way"), never as a spec for "how Vinnstack actually works." doc/vinnstack-bdd-pipeline.html (added 2026-07-12) documents the real, code-grounded pipeline instead.

## Related
[[Vinnstack vinnstack-data-model.html predates the BDD workspace]]
[[Vinnstack AI calls are stateless headless claude CLI runs, not an agent runtime]]
