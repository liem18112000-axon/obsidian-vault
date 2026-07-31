---
title: "Technical interrogation questions must pass the 2-minute tech-lead test"
created: 2026-07-21
type: lesson
status: seedling
source: "session 2026-07-21, LUZ-156281 developer feedback"
tags: [vinnstack, interrogation, skill-design, ai-pipeline]
---

# Technical interrogation questions must pass the 2-minute tech-lead test

An AI-generated technical interrogation question is only useful if a tech lead can answer it in a refinement meeting, in roughly two minutes, from experience — without opening the code. Developer feedback on epic LUZ-156281 exposed the failure modes: project-sequencing choices ("build a placeholder now or wait for the CFO?") dressed up as engineering decisions, detail-level asks (payload fields, timing mechanics, config formats), and sequence/gantt diagrams that walk through steps instead of contrasting solution shapes.

The vinnstack `interrogate-technical` skill v2 enforces this as an **altitude rule**: a question must decide the solution shape at module/service level — which approach/pattern, which module owns what, what is new vs reused. Anything below that line the AI resolves itself and records as an assumption (see [[Let developers veto stated assumptions instead of designing details]]). The output now leads with a proposed high-level solution plus one module-based mermaid flowchart (existing vs NEW components marked), hard-capped at 7 questions, with sequence diagrams and gantt charts banned as question aids.

## Related

- [[Let developers veto stated assumptions instead of designing details]]
