---
title: "Comparison narratives invent deltas - anchor every claimed change to the machine diff"
created: 2026-07-23
type: lesson
status: seedling
source: "session 2026-07-23, S1 flow comparison verify pass"
tags: [llm, verification, diff, writing, gotcha]
---

# Comparison narratives invent deltas - anchor every claimed change to the machine diff

When writing a v1-vs-v2 comparison of two documents, an LLM (me) will occasionally attribute a change to the newer version that the diff does not support — a "false delta." On the S1 flow comparison I wrote "v2 even sharpened the risk wording ('primary delivery risk')" when that sentence was **byte-identical in both versions** (a context line in the unified diff, no +/-). Worse, the false delta contradicted my own report's "stayed identical" section.

Two guards that caught it:
1. **Generate a machine `diff -u` first and treat it as ground truth.** Every claimed change must map to a +/- line; anything on a context (leading-space) line is unchanged — do not narrate it as a change.
2. **Adversarial verify with two lenses:** an accuracy checker (quotes/claims vs source files) and a completeness checker (diff vs report, both directions — missed real changes AND invented ones). Both independently flagged the same false delta plus an over-stated "self-contradiction" (v1's table cell already carried a '(see Open item 5)' hedge) and a "(unchanged content)" label that hid a minor reword.

Rule: in a diff report, calibrate the verb to the diff line type — 'unchanged' for context lines, 'reworded' for cosmetic +/- , 'changed/added' only for substantive +/-. Round-trip the finished narrative back through the verifier before shipping.

## Related

- [[A refine regenerate can close open items from evidence the first pass left unexploited]]
