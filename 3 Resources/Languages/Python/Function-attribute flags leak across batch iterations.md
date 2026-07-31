---
title: "Function-attribute flags leak across batch iterations"
created: 2026-06-10
type: lesson
status: seedling
source: "code-review session 2026-06-10, fb-info-project"
tags: [python, state, batch, gotcha]
---

# Function-attribute flags leak across batch iterations

A flag stored as a function attribute (`func.seen = False` at module import, set `True` at runtime) is process-global state. In a batch loop that processes many items in one process, the flag carries over from item 1 to item 2+: warn-once logic fires once per *process* instead of once per *item*, and any error message keyed on the flag misattributes item 2's failure to something that actually happened on item 1.

Fix: reset such flags at the start of each unit of work (each loop item / each scrape), or scope the state to an object created per item instead of the module.

Found in a scraper where `close_login_popup.seen` from link 1 made link 2's unrelated timeout report 'session expired'.

## Related

- [[JSON null survives str() as 'None' and corrupts lookup-table normalization]]
