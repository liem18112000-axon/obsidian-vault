---
ai_hash: 787a01456d96f711
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-23
entities:
- fb-info-project
- profile UUIDs
- combined workbook
- Facebook scraper
- batch
- output .xlsx
- input link
- feat/profile-uuid-single-sheet
- bare FB id
- profile UUID
- full http(s) URL
- profile links
- output/profiles_<stamp>.xlsx
- read_input
- combine=True
- profile.php?id=
- http
- link_from_cell
- service.batch
- combined rows
- excel_io.save_combined
- save_template
- _row_cells
- 11-column template
- combined filename
- checkpoint
- combined_output
- set_combined_output
- resumed run
- Reconstitute done items from the run cache when rewriting an aggregated output file
  on resume
- resume-safety trick
- Verify the Quê quán hometown fix by asserting no output row has Quê quán equal to
  Name
source: session 2026-06-23
status: seedling
tags:
- fb-info-project
- facebook-scraper
- excel
- design
title: fb-info-project merges bare-id 'profile UUID' inputs into one combined workbook
type: howto
---

# fb-info-project merges bare-id 'profile UUID' inputs into one combined workbook

In the fb-info-project Facebook scraper, the batch normally writes one output .xlsx per input link. New behavior (branch `feat/profile-uuid-single-sheet`): when an input cell is a **bare numeric FB id** (the user's "profile UUID", e.g. 61572037853411) rather than a full http(s) URL, all such profile links are merged into ONE combined workbook `output/profiles_<stamp>.xlsx`. Links typed as full URLs keep one-file-per-link.

Detection lives in `read_input`: a row gets `combine=True` when its produced URL contains `profile.php?id=` AND the raw cell did not start with http — i.e. `link_from_cell` turned a bare id into a profile link. A full `profile.php?id=` URL typed by hand is NOT combined (raw started with http).

Implementation: `service.batch` accumulates combined rows as (n, link, title, rows) in input order and rewrites the single file via `excel_io.save_combined` after each such link completes. `save_combined` and `save_template` share a `_row_cells` helper so the 11-column template stays identical. The combined filename is stored in the checkpoint (`combined_output`/`set_combined_output`) so a resumed run overwrites the same file.

See [[Reconstitute done items from the run cache when rewriting an aggregated output file on resume]] for the resume-safety trick.

## Related

- [[Reconstitute done items from the run cache when rewriting an aggregated output file on resume]]
- [[Verify the Quê quán hometown fix by asserting no output row has Quê quán equal to Name]]

%% ai-graph-start %%

**Related notes:**
- [[Reconstitute done items from the run cache when rewriting an aggregated output file on resume]]
- [[fb-scraper writes output per link only at the end; killing mid-run loses the whole link]]
- [[FB photofbid= links scrape as post mode; filename id falls back to na]]
- [[Verify the Quê quán hometown fix by asserting no output row has Quê quán equal to Name]]
- [[Crash-safe incremental output as_completed + indexed results + stable filename reused for checkpoint and final]]

**Relations:**
- fb-info-project — *merges* — profile UUIDs
- profile UUIDs — *into* — combined workbook
- fb-info-project — *is a* — Facebook scraper
- batch — *writes* — output .xlsx
- output .xlsx — *per* — input link
- feat/profile-uuid-single-sheet — *enables merging of* — profile links
- profile links — *into* — output/profiles_<stamp>.xlsx
- bare FB id — *is also known as* — profile UUID
- read_input — *detects* — bare FB id
- read_input — *sets* — combine=True
- combine=True — *if URL contains* — profile.php?id=
- combine=True — *if raw cell not starting with* — http
- link_from_cell — *converts* — bare FB id
- bare FB id — *to* — profile link
- service.batch — *accumulates* — combined rows
- service.batch — *rewrites* — combined workbook
- combined workbook — *using* — excel_io.save_combined
- excel_io.save_combined — *shares helper* — _row_cells
- _row_cells — *with* — save_template
- _row_cells — *ensures consistency of* — 11-column template
- combined filename — *stored in* — checkpoint
- checkpoint — *uses* — combined_output
- checkpoint — *uses* — set_combined_output
- resumed run — *overwrites* — combined filename
- Reconstitute done items from the run cache when rewriting an aggregated output file on resume — *explains* — resume-safety trick
- fb-info-project — *references* — Reconstitute done items from the run cache when rewriting an aggregated output file on resume
- fb-info-project — *references* — Verify the Quê quán hometown fix by asserting no output row has Quê quán equal to Name

%% ai-graph-end %%