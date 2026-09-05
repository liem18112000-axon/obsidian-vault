---
title: "Mermaid node labels with parentheses or special chars must be wrapped in double quotes"
created: 2026-08-07
type: gotcha
status: seedling
source: "luz_docs_import review diagram 2026-08-07"
tags: [mermaid, markdown, diagrams, gotcha]
---

# Mermaid node labels with parentheses or special chars must be wrapped in double quotes

In Mermaid flowchart node labels, characters that the parser also uses as shape/syntax tokens must be escaped by wrapping the whole label in double quotes: `D["set createJob (status UPLOADED)"]`. An unquoted `(` inside `[...]` makes Mermaid think a new shape is starting and throws e.g. `Parse error ... got 'PS'` (PS = paren-start; likewise 'SQS' for '['). 

Trigger chars include parentheses `()`, square/curly brackets, and often `/` combos. Safe fix: quote any label containing punctuation. `<br/>` line breaks and plain `/` are usually tolerated unquoted, but quoting is the reliable default.

Gotcha within the gotcha: the parser stops at the FIRST offending node, so after quoting one you may hit the next. Scan ALL nodes for unescaped special chars in one pass rather than fixing one-at-a-time. Applies to Mermaid rendered in Claude artifacts and in Obsidian/GitHub Markdown alike.

Related: [[luz_docs_import]].
