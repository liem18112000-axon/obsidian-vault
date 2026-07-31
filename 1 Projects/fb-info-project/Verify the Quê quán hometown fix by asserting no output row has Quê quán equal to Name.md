---
title: "Verify the Quê quán hometown fix by asserting no output row has Quê quán equal to Name"
created: 2026-06-22
type: howto
status: seedling
source: "session 2026-06-22"
tags: [fb-info-project, testing, facebook-scraper, verification]
---

# Verify the Quê quán hometown fix by asserting no output row has Quê quán equal to Name

The hometown bug in the fb-info-project scraper: a Facebook profile's "See more from <Name>" link leaked the profile's own display name into the **Quê quán** (hometown) output column. The reliable regression check is data-level, not visual:

1. Run a real batch scrape: `python scraper.py batch --input resources/test.xlsx`.
2. Open the newest `output/*.xlsx`.
3. Assert **zero** rows where `Quê quán == Name` (trimmed). Any nonzero count means the leak is back.

Pre-fix output showed profile names like "Việt Tân" / "Duong Nguyen" sitting in Quê quán; post-fix output holds only genuine place names (Tây Ninh, Hanoi, Ho Chi Minh City…) or stays blank.

Backdrop: a live scrape emits Playwright `page.goto`/`inner_text` timeouts and occasional `ERR_CONNECTION_RESET` on individual profiles — that's normal Facebook rate-limiting, not a regression. Those profiles just come back with empty location fields and the batch still completes.

Fixed on branch `fix/hometown-see-more-from` (PR #5), commit 35ff8e8.

## Related

- [[A test-output file is only valid evidence if its mtime postdates the fix commit]]
