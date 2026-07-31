---
title: "fb-info-project merges bare-id 'profile UUID' inputs into one combined workbook"
created: 2026-06-23
type: howto
status: seedling
source: "session 2026-06-23"
tags: [fb-info-project, facebook-scraper, excel, design]
---

# fb-info-project merges bare-id 'profile UUID' inputs into one combined workbook

In the fb-info-project Facebook scraper, the batch normally writes one output .xlsx per input link. New behavior (branch `feat/profile-uuid-single-sheet`): when an input cell is a **bare numeric FB id** (the user's "profile UUID", e.g. 61572037853411) rather than a full http(s) URL, all such profile links are merged into ONE combined workbook `output/profiles_<stamp>.xlsx`. Links typed as full URLs keep one-file-per-link.

Detection lives in `read_input`: a row gets `combine=True` when its produced URL contains `profile.php?id=` AND the raw cell did not start with http — i.e. `link_from_cell` turned a bare id into a profile link. A full `profile.php?id=` URL typed by hand is NOT combined (raw started with http).

Implementation: `service.batch` accumulates combined rows as (n, link, title, rows) in input order and rewrites the single file via `excel_io.save_combined` after each such link completes. `save_combined` and `save_template` share a `_row_cells` helper so the 11-column template stays identical. The combined filename is stored in the checkpoint (`combined_output`/`set_combined_output`) so a resumed run overwrites the same file.

See [[Reconstitute done items from the run cache when rewriting an aggregated output file on resume]] for the resume-safety trick.

## Related

- [[Reconstitute done items from the run cache when rewriting an aggregated output file on resume]]
- [[Verify the Quê quán hometown fix by asserting no output row has Quê quán equal to Name]]
