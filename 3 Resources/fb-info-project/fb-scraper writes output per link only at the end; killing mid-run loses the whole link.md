---
title: "fb-scraper writes output per link only at the end; killing mid-run loses the whole link"
created: 2026-06-14
type: lesson
status: seedling
source: "live run 2026-06-14"
tags: [fb-info-project, architecture, gotcha, output]
---

# fb-scraper writes output per link only at the end; killing mid-run loses the whole link

In fb-info-project, `src/service.batch` writes one output workbook **per input link via `save_template`, called only after every collected commenter profile for that link has been visited**. There is no incremental/partial save during the profile-visit loop.

Consequence: if you kill the run (or it crashes) while still visiting profiles, **the entire link's work is lost** — no file is produced, even if 100+ profiles were already scraped and logged. Verified 2026-06-14: a capped run reached profile [111/209] with 19 locations already in the log, was stopped, and wrote **no** output file.

Implication for testing/operations: a long link must run to completion to yield anything. If you need a quick result, pick input with few commenters rather than expecting a partial dump. A potential improvement would be to flush rows to the workbook incrementally (e.g. after each profile or every N profiles).

## Related

- [[--max-expand caps comment batches not profile count; profile-visit phase dominates runtime]]
