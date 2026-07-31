---
title: "Reconstitute done items from the run cache when rewriting an aggregated output file on resume"
created: 2026-06-23
type: lesson
status: seedling
source: "session 2026-06-23"
tags: [resume, checkpointing, batch-processing, gotcha]
---

# Reconstitute done items from the run cache when rewriting an aggregated output file on resume

When a resumable batch aggregates many per-item results into ONE output file that is **rewritten from scratch** each time an item completes, a naive in-memory accumulator loses items finished in a *prior* run: on resume those items are marked done and skipped, so they never re-enter the accumulator, and the next rewrite silently drops them.

Fix: at startup, **reconstitute** the already-done items' rows from whatever durable state the resume mechanism already keeps — typically the run's profile/result cache keyed by a stable id. Seed the accumulator with those reconstituted rows (in input order) before processing the still-pending items. Then every rewrite contains prior-run + this-run work.

This only works because the cache stores *successful* results keyed stably (here: profile_url == the profile.php?id= link, which is never redirect-rewritten for bare-id inputs), so a done item is guaranteed present in the cache. Contrast with the one-file-per-item pattern, where each item owns its file and a rewrite can never clobber another item's output — there the problem doesn't arise.

Concrete instance: [[fb-info-project merges bare-id 'profile UUID' inputs into one combined workbook]].

## Related

- [[fb-info-project merges bare-id 'profile UUID' inputs into one combined workbook]]
