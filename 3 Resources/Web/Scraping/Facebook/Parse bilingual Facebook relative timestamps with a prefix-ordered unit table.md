---
ai_hash: df8819e18f0eabc0
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-11
entities: []
source: fb-info-project session 2026-06-11
status: seedling
tags:
- facebook
- parsing
- regex
- python
- vietnamese
title: Parse bilingual Facebook relative timestamps with a prefix-ordered unit table
type: howto
---

# Parse bilingual Facebook relative timestamps with a prefix-ordered unit table

Facebook shows comment ages as short relative strings in the UI language — VI: "2 giờ", "1 tuần trước", "vừa xong"; EN: "3d", "5 mins ago", "just now". To convert to seconds, match `^(\d+)\s*(unit)(\s*(trước|ago))?$` and map the unit through a **prefix table** checked with `startswith`.

The prefix order is load-bearing:
- `mo` (months) must be tried **before** `m` (minutes), or "10mo" parses as 10 minutes;
- `sec` before `s`, `min` before `m` likewise.
- Vietnamese units (giây/phút/giờ/ngày/tuần/tháng/năm) are unambiguous and can go first.

Return `None` for non-timestamps, and when sorting by age use `key=lambda x: age if age is not None else float('inf')` — Python's stable sort then sinks unknown-age items to the end while preserving their original (DOM) order.

## Related

- [[Verify Facebook comment sort switch by re-reading the sort button label]]
- [[asyncio.gather preserves input order in its result list]]

%% ai-graph-start %%

**Related notes:**
- _(none above threshold)_

%% ai-graph-end %%