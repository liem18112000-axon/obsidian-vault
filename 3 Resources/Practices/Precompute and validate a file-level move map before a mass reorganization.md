---
ai_hash: cad2e9cc4a172f60
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-31
entities: []
source: vault PARA reorganization 2026-07-31
status: seedling
tags:
- refactoring
- filesystem
- git
- safety
title: Precompute and validate a file-level move map before a mass reorganization
type: lesson
---

# Precompute and validate a file-level move map before a mass reorganization

Never move files as you decide where they go. Build the complete **file-level** source→destination map first, validate it, and only then execute — otherwise a half-finished run leaves the tree in a state where you can no longer tell what was moved.

Four checks that must all pass before the first move:

1. every source path still exists
2. no two sources map to the same destination
3. no destination already exists on disk (unless it is itself a source being moved out)
4. nothing in scope is left unmapped

Building the map from the *original* on-disk state also means execution order stops mattering.

**Expand folder rules deepest-first.** With rules like `AI Tools → AI` and `AI Tools/Claude Code → AI/Claude-Code`, sort by path depth descending and let each rule claim files into a "already claimed" set. The specific subfolder rule takes its files before the parent rule sweeps the remainder.

This caught real problems: on a 1100-file vault reorganization the validation pass reported zero collisions across three separate batches, and every one of the 947 moves in the largest batch landed correctly. Use `git mv` so history follows the file and you get rename detection for free — that rename map is exactly what you need afterwards to repair links.

## Related

- [[Measure a broken-link baseline before a mass vault refactor]]
- [[Assigning to an awk field rebuilds $0 with OFS and destroys the separator]]

%% ai-graph-start %%

**Related notes:**
- [[Measure a broken-link baseline before a mass vault refactor]]
- [[Assigning to an awk field rebuilds $0 with OFS and destroys the separator]]
- [[Obsidian plugin configs pin folders to exact vault-root paths]]

%% ai-graph-end %%