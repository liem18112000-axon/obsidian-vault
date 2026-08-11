---
ai_hash: 6d5caf2c6e513138
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-21
entities:
- Technical interrogation questions
- 2-minute tech-lead test
- AI-generated technical interrogation question
- tech lead
- refinement meeting
- Developer feedback
- epic LUZ-156281
- failure modes
- project-sequencing choices
- engineering decisions
- detail-level asks
- payload fields
- timing mechanics
- config formats
- sequence diagrams
- gantt diagrams
- solution shapes
- vinnstack interrogate-technical skill v2
- altitude rule
- module/service level
- approach/pattern
- module ownership
- new vs reused
- AI
- assumption
- high-level solution
- module-based mermaid flowchart
- existing components
- NEW components
- 7 questions
- question aids
- Let developers veto stated assumptions instead of designing details
source: session 2026-07-21, LUZ-156281 developer feedback
status: seedling
tags:
- vinnstack
- interrogation
- skill-design
- ai-pipeline
title: Technical interrogation questions must pass the 2-minute tech-lead test
type: lesson
---

# Technical interrogation questions must pass the 2-minute tech-lead test

An AI-generated technical interrogation question is only useful if a tech lead can answer it in a refinement meeting, in roughly two minutes, from experience — without opening the code. Developer feedback on epic LUZ-156281 exposed the failure modes: project-sequencing choices ("build a placeholder now or wait for the CFO?") dressed up as engineering decisions, detail-level asks (payload fields, timing mechanics, config formats), and sequence/gantt diagrams that walk through steps instead of contrasting solution shapes.

The vinnstack `interrogate-technical` skill v2 enforces this as an **altitude rule**: a question must decide the solution shape at module/service level — which approach/pattern, which module owns what, what is new vs reused. Anything below that line the AI resolves itself and records as an assumption (see [[Let developers veto stated assumptions instead of designing details]]). The output now leads with a proposed high-level solution plus one module-based mermaid flowchart (existing vs NEW components marked), hard-capped at 7 questions, with sequence diagrams and gantt charts banned as question aids.

## Related

- [[Let developers veto stated assumptions instead of designing details]]

%% ai-graph-start %%

**Related notes:**
- [[Let developers veto stated assumptions instead of designing details]]
- [[interrogate-qa skill built standalone due to track CHECK constraint]]
- [[interrogate-qa is cross-cutting across all Epics, Stories, and Flows]]
- [[Action Points]]
- [[Find then adversarial-refute verify pass cuts AI reviewer false positives]]

**Relations:**
- Technical interrogation questions — *must pass* — 2-minute tech-lead test
- AI-generated technical interrogation question — *is useful if answerable by* — tech lead
- tech lead — *answers in* — refinement meeting
- tech lead — *answers within* — 2-minute tech-lead test
- Developer feedback — *on* — epic LUZ-156281
- Developer feedback — *exposed* — failure modes
- failure modes — *include* — project-sequencing choices
- project-sequencing choices — *dressed up as* — engineering decisions
- failure modes — *include* — detail-level asks
- detail-level asks — *comprise* — payload fields
- detail-level asks — *comprise* — timing mechanics
- detail-level asks — *comprise* — config formats
- failure modes — *include* — sequence diagrams
- failure modes — *include* — gantt diagrams
- sequence diagrams — *walk through* — steps
- gantt diagrams — *walk through* — steps
- vinnstack interrogate-technical skill v2 — *enforces* — altitude rule
- altitude rule — *requires questions to decide* — solution shapes
- solution shapes — *at* — module/service level
- module/service level — *covers* — approach/pattern
- module/service level — *covers* — module ownership
- module/service level — *covers* — new vs reused
- AI — *resolves details below* — module/service level
- AI — *records as* — assumption
- assumption — *is explained in* — Let developers veto stated assumptions instead of designing details
- vinnstack interrogate-technical skill v2 — *produces* — high-level solution
- vinnstack interrogate-technical skill v2 — *produces* — module-based mermaid flowchart
- module-based mermaid flowchart — *marks* — existing components
- module-based mermaid flowchart — *marks* — NEW components
- vinnstack interrogate-technical skill v2 — *limits output to* — 7 questions
- sequence diagrams — *banned as* — question aids
- gantt diagrams — *banned as* — question aids

%% ai-graph-end %%