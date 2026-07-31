---
ai_hash: 5b3be0913ec5de23
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-22
entities: []
source: session 2026-06-22
status: seedling
tags:
- testing
- gotcha
- verification
title: A test-output file is only valid evidence if its mtime postdates the fix commit
type: lesson
---

# A test-output file is only valid evidence if its mtime postdates the fix commit

When using a generated artifact (e.g. a scraper's `output/*.xlsx`) as evidence that a fix works, the artifact is only valid if its **file mtime postdates the fix commit time**. An output file produced before the fix commit still reflects the buggy code path, so reading it "confirms" nothing — it will show the old behavior regardless of the current source.

Practical rule: before trusting an existing output file, compare `git show -s --format=%ci <fix-commit>` against the file's mtime. If the file is older, **regenerate** it by re-running the tool, then inspect the fresh artifact.

This bit during the fb-info-project hometown fix: a stale `output/` xlsx from earlier the same day still showed the Quê-quán-equals-name bug even though the fix was already committed — the file simply predated the commit.

## Related

- [[Verify the Quê quán hometown fix by asserting no output row has Quê quán equal to Name]]

%% ai-graph-start %%

**Related notes:**
- [[Verify the Quê quán hometown fix by asserting no output row has Quê quán equal to Name]]
- [[Black-box artifact E2E drive a committed sample fixture so the only live input is the secret]]

%% ai-graph-end %%