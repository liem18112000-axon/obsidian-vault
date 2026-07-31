---
ai_hash: a8e3b677e8694347
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-15
entities: []
source: fb-info-project pause/resume, 2026-06-15
status: seedling
tags:
- checkpoint
- atomic-write
- resilience
- file-io
- resume
title: 'Checkpoint files: atomic tmp+rename write plus an input fingerprint'
type: lesson
---

# Checkpoint files: atomic tmp+rename write plus an input fingerprint

A durable checkpoint/state file needs two hygiene properties so it is trustworthy rather than a liability:

**1. Atomic write (tmp + rename).** Write the new content to a sibling `.tmp` file, then `os.replace()` (atomic rename) it over the real file. A crash *during* the write then leaves either the old complete file or the new complete file — never a half-written, unparseable one. Writing in place risks corrupting the only copy of your progress exactly when you most need it.

```python
tmp = file.with_name(file.name + '.tmp')
tmp.write_text(json.dumps(data))
tmp.replace(file)   # atomic on POSIX and Windows
```

**2. Fingerprint the inputs to reject stale state.** Store a hash (e.g. sha256) of the identities of the work-items the checkpoint describes. On load, recompute and compare; on mismatch, discard the checkpoint and start fresh instead of mis-applying it. This stops 'I edited the input then resumed' from silently mapping old progress onto a different work-set.

**Context:** `src/checkpoint.py` in fb-info-project writes `output/.resume-<input>.json` atomically and fingerprints all link identities; changing the input workbook invalidates the old checkpoint. The file is created while a run is incomplete and deleted on clean completion, so a finished run leaves nothing behind.

Related: [[A persisted dedup cache doubles as a resume log]], [[A resume must not re-charge one-time accounting]]

## Related

- [[A persisted dedup cache doubles as a resume log]]
- [[A resume must not re-charge one-time accounting]]

%% ai-graph-start %%

**Related notes:**
- [[Test resume by pre-seeding a checkpoint, not by simulating an interrupt]]
- [[A persisted dedup cache doubles as a resume log]]
- [[Crash-safe incremental output as_completed + indexed results + stable filename reused for checkpoint and final]]
- [[A resume must not re-charge one-time accounting]]
- [[Reconstitute done items from the run cache when rewriting an aggregated output file on resume]]

%% ai-graph-end %%