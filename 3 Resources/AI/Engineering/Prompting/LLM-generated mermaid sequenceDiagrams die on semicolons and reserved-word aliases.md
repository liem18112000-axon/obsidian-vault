---
title: "LLM-generated mermaid sequenceDiagrams die on semicolons and reserved-word aliases"
created: 2026-07-03
type: gotcha
status: seedling
source: "session 2026-07-03, vinnstack sequence-diagram failures"
tags: [mermaid, llm, diagrams, vinnstack]
---

# LLM-generated mermaid sequenceDiagrams die on semicolons and reserved-word aliases

Two mermaid landmines that LLMs hit constantly when writing sequenceDiagrams (observed: EVERY generated sequence diagram in a batch failed on one of them, while flowcharts were fine):

1. **Semicolon in message text.** Mermaid treats `;` as a statement terminator, so `A->>B: no QR; mark state` truncates the line and the parser explodes later ("Expecting arrow ... at 'end'"). Fix: replace `;` with `,` in the text after the first `:` on arrow lines.
2. **Reserved word as participant id/alias.** Block keywords (`opt`, `alt`, `else`, `loop`, `par`, `end`, `note`, ...) are lexed case-insensitively, so `participant OPT as Optimus` makes every later `X-->>OPT:` fail with "Expecting ACTOR, got 'opt'". Fix: rename the id (`OPT_`) everywhere.

Defense in depth: (a) a mechanical `repairMermaid()` pass at render time, attempted ONLY after the original source fails to parse (conservative; copy-code still copies the original), and (b) name the two landmines explicitly in the generation prompt contract - "valid Mermaid" alone does not prevent them.

## Related

- [[AI self-critique loop - a post-generation critic pass rates the artifact and feeds the next run]]
