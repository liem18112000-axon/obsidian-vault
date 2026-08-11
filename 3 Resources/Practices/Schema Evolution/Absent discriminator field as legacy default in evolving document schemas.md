---
ai_hash: 61355d67a8772ec0
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-05
entities: []
source: session 2026-06-05 LUZ-154804
status: seedling
tags:
- schema-evolution
- mongodb
- design-decision
title: Absent discriminator field as legacy default in evolving document schemas
type: lesson
---

# Absent discriminator field as legacy default in evolving document schemas

When adding a discriminator to a document schema that already has rows in production, treat the *absent* field as the legacy variant instead of backfilling or introducing an enum value that is never written. New variants get an explicit marker value; old rows keep meaning what they always meant.

Why: a backfill is a migration you don't need, and an enum whose default member (`FOLDER_RENAME`) never appears in storage misleads readers into searching for writes that don't exist. Even better than a dedicated type column: when the new variant carries a payload field the legacy variant never writes, **the payload's existence *is* the discriminator** — `{field: {$exists: true/false}}` splits the kinds at query level and no extra column is needed at all.

Example: luz_docs `materializeCascade` markers — document-rematerialize markers carry `documentIds`; folder-rename markers never write it. `readPartialCascadeMarkers` filters `documentIds $exists:false`, `readPartialDocumentRematerializeMarkers` filters `$exists:true`, so each retry path only ever sees its own marker kind (an explicit `type` column was added first, then deleted as redundant).

Related: [[luz_docs materialize passive retry via cascade markers]]

## Related

- [[luz_docs materialize passive retry via cascade markers]]

%% ai-graph-start %%

**Related notes:**
- [[luz_docs materialize passive retry via cascade markers]]
- [[Don't share one predicate between a read-path gate and a backfill selector]]
- [[Cascade-marker pattern for crash-safe async retry]]
- [[Migration campaign status can silently drift from real document state]]
- [[luz_docs has two materialize cascade delivery mechanisms]]

%% ai-graph-end %%