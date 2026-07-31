---
ai_hash: dcbe62c77600d524
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-01
entities: []
source: session 2026-07-01 (Vinnstack SkillsView)
status: seedling
tags:
- react
- markdown
- react-markdown
- frontmatter
- gotcha
- vinnstack
title: react-markdown renders YAML frontmatter as literal text — strip it before rendering
type: lesson
---

# react-markdown renders YAML frontmatter as literal text — strip it before rendering

react-markdown (remark/rehype) has **no built-in frontmatter support**: a leading YAML `---\n…\n---` block is rendered as literal text (the first `---` often becomes a thematic break, the rest as a paragraph). So a Markdown file with frontmatter shown via <Markdown text={content}/> displays `name: … description: … tags: […]` verbatim on screen.

**Fix options:**
1. Strip the fence before rendering: `md.replace(/^\uFEFF?\s*---\r?\n[\s\S]*?\r?\n---\r?\n?/, '')` (handles BOM + CRLF). Cheap, no dep — good when the meta is shown elsewhere (e.g. a panel header already shows name/description).
2. Add `remark-frontmatter` so it's parsed into a node (then optionally rendered).

Real case: Vinnstack SkillsView rendered SKILL.md files raw, so every skill's YAML header leaked into the body. Fixed by stripping in the client component (one place covers all skill sources), leaving the server APIs untouched. Note a similar server-side strip already existed in interrogationRunner.loadSkill — but sharing a one-line regex across the client/server boundary wasn't worth a util.

General gotcha: any renderer of user/authored Markdown that may carry frontmatter must decide to strip or parse it — never assume the library hides it.

%% ai-graph-start %%

**Related notes:**
- [[react-markdown without rehype-raw silently drops HTML blocks and swallows following lines]]

%% ai-graph-end %%