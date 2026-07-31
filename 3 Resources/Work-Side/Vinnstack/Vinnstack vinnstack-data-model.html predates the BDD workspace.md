---
ai_hash: 1fcea47da48e207f
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-12
entities: []
source: session 2026-07-12 — writing doc/vinnstack-bdd-pipeline.html
status: seedling
tags:
- vinnstack
- docs
- gotcha
title: Vinnstack vinnstack-data-model.html predates the BDD workspace
type: observation
---

# Vinnstack vinnstack-data-model.html predates the BDD workspace

doc/vinnstack-data-model.html in the Vinnstack repo lists only 11 skills and embeds them verbatim, but it predates commit 115c582 ("Add Behaviour Driven Test workspace"), added 2026-07 (Vinnstack). It is missing story-to-bdd-scenarios, implement-bdd-steps, story-to-process-flow, interrogate-qa, and critique-artifact — all real, wired-in skills that exist today (21 skill directories total under vinnstack-skills/ as of 2026-07-12).

Before citing that doc as authoritative on "what skills exist," check the live vinnstack-skills/ directory listing instead — the doc needs a refresh to reflect the BDD workspace.

## Related
[[Vinnstack ai-framework.html is aspirational, not the real code]]

%% ai-graph-start %%

**Related notes:**
- [[Vinnstack ai-framework.html is aspirational, not the real code]]
- [[vinnstack BDD pipeline stops at JiraXray, never writes files into a cloned repo]]
- [[Vinnstack withholds gitgh from the model in BDD step implementation]]
- [[Vinnstack AI calls are stateless headless claude CLI runs, not an agent runtime]]
- [[interrogate-qa is cross-cutting across all Epics, Stories, and Flows]]

%% ai-graph-end %%