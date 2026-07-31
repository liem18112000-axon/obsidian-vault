---
title: "A test-output file is only valid evidence if its mtime postdates the fix commit"
created: 2026-06-22
type: lesson
status: seedling
source: "session 2026-06-22"
tags: [testing, gotcha, verification]
---

# A test-output file is only valid evidence if its mtime postdates the fix commit

When using a generated artifact (e.g. a scraper's `output/*.xlsx`) as evidence that a fix works, the artifact is only valid if its **file mtime postdates the fix commit time**. An output file produced before the fix commit still reflects the buggy code path, so reading it "confirms" nothing — it will show the old behavior regardless of the current source.

Practical rule: before trusting an existing output file, compare `git show -s --format=%ci <fix-commit>` against the file's mtime. If the file is older, **regenerate** it by re-running the tool, then inspect the fresh artifact.

This bit during the fb-info-project hometown fix: a stale `output/` xlsx from earlier the same day still showed the Quê-quán-equals-name bug even though the fix was already committed — the file simply predated the commit.

## Related

- [[Verify the Quê quán hometown fix by asserting no output row has Quê quán equal to Name]]
