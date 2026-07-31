---
title: "earchive-data-prepare logs document progress only every 10 batches"
created: 2026-07-10
type: lesson
status: seedling
source: "session 2026-07-10"
tags: [earchive, mongodb, skill-gotcha, progress-logging]
---

# earchive-data-prepare logs document progress only every 10 batches

The earchive-data-prepare skill'\''s `prepare_data.js` only prints a document-insert progress line every `BATCH_SIZE * 10` documents (default BATCH_SIZE=1000 → every 10,000 docs), or once at the very end when `inserted === docCount`. So a run that looks silent for many minutes after the first batch is not stuck — it just hasn'\''t hit the next log checkpoint yet.

To check liveness during a silent stretch, don'\''t wait on log lines. Instead check that the `node.exe` process for the run is still alive (RSS should be sizeable and growing, since it holds a full `docCount`-sized batch of faker-generated docs in memory) and that its `kubectl port-forward` companion process is still running.

This compounds with the known throughput bottleneck documented in [[earchive-prepare-knobs]]: faker text generation runs at only ~25-30 docs/s with the default `TEXT_PARAGRAPHS=100`. That means the first 10,000-doc checkpoint alone can take ~6-7 minutes of apparent silence, which is easy to mistake for a hang.

## Related

- [[earchive-prepare-knobs]]
