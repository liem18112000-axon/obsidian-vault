---
ai_hash: ff22d6842023ef77
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-14
entities: []
source: session 2026-07-14
status: seedling
tags:
- react-markdown
- markdown
- rendering
- gotcha
- commonmark
title: react-markdown without rehype-raw silently drops HTML blocks and swallows following
  lines
type: lesson
---

# react-markdown without rehype-raw silently drops HTML blocks and swallows following lines

Gotcha (Vinnstack Polaris agent viewer, 2026-07-14): react-markdown WITHOUT the rehype-raw plugin doesn't merely ignore raw HTML — a line that is a lone tag like <rules> or <workflow> is parsed by CommonMark as an HTML BLOCK, so react-markdown DROPS it AND swallows the following lines (until a blank line) as part of that block. Content using XML-ish structural tags (common in LLM prompt/agent definitions) therefore renders broken — whole sections and fenced code examples vanish — while sibling docs with no such tags render fine. That is why one agent brief broke and the others didn't.

Fix used: escape the HTML-block trigger by replacing "<" with "&lt;" OUTSIDE fenced code (track fence state line-by-line; leave code and the fence lines themselves untouched). "&lt;rules>" then renders as the literal text "<rules>" and no longer starts a block. Escape ONLY "<" (not ">") so markdown blockquotes still work, and skip inside fences so code shows verbatim.

Alternative (riskier): enable rehype-raw + rehype-sanitize globally — but that widens the XSS surface for any user/LLM-supplied markdown, so prefer the scoped escape when the content is really prompt text, not authored HTML.

Related: [[Polaris MCP is search-only 5 tools, no list-all, driven over HTTP JSON-RPC not the CLI]].

%% ai-graph-start %%

**Related notes:**
- [[react-markdown renders YAML frontmatter as literal text — strip it before rendering]]

%% ai-graph-end %%