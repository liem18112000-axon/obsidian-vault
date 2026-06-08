---
title: "Whether matchedCount>modifiedCount (HTTP 207) is benign depends on filter tightness, not the status code"
created: 2026-06-08
type: lesson
status: seedling
source: "luz_docs materialize review #25, 2026-06-08"
tags: [mongodb, jsonstore, luz-docs, materialize, correctness, gotcha]
---

# Whether matchedCount>modifiedCount (HTTP 207) is benign depends on filter tightness, not the status code

A bulk update reporting `matchedCount > modifiedCount` (luz_jsonstore surfaces this as HTTP 207 SC_MULTI_STATUS) has **two indistinguishable causes**: (a) matched docs already held the target value → correctly unchanged (benign); (b) matched docs were *supposed* to change but the write did not apply → genuine partial failure (stale data left behind). The count alone cannot tell them apart. Whether 207 is safe to treat as benign is decided by **how tight the update filter is**, NOT by the status code.

## The rule
- **Tight filter** — matches only docs that still NEED the change (filter encodes a "stale" predicate). Then every matched doc MUST be modified, so `matched > modified` is *unambiguously* a real partial failure → must retry / mark PARTIAL / sweep. A converged run matches 0 docs → clean 200.
- **Loose filter** — matches a superset (e.g. "all docs in these folders"). Then `matched > modified` is the NORMAL case and is indistinguishable from a real miss. Swallowing 207 as benign here **silently drops stragglers**; retrying forever here loops (deterministic re-run reproduces it). Neither blanket policy is correct — you must disambiguate by re-querying the still-stale set (`count(stale)==0` ⇒ converged), or tighten the filter.

## Concrete instance — luz_docs materialize cascades
Two sibling cascades, opposite filter tightness:
- **Rename** `buildFolderRenameFilter` is TIGHT (`slot != newName`) → 207 = real partial → `return false` → PARTIAL marker → sweeper. Sound.
- **Parent-change** `buildFolderParentChangeFilter` is LOOSE (`folderIds IN affected`) → 207 is normal AND ambiguous. The "treat 207 as benign no-op" fix (`MaterializeMultiStatusException` swallowed) masks a genuine 8k-of-10k partial write. Logged as review finding #25. Fix options: (A) tighten the filter to a sentinel-stale predicate so 207 regains a single meaning; (B) keep loose filter, treat 207 as PARTIAL, judge convergence by re-querying remaining-stale docs.

## Related
[[Deterministic Mongo pipeline updates return matched-not-modified; treat jsonstore SC_MULTI_STATUS as benign]]
[[Encode a benign-error decision in a dedicated exception type, not a swallowed catch on a magic status code]]
[[materialize-code-review]]

## Related

- [[Deterministic Mongo pipeline updates return matched-not-modified; treat jsonstore SC_MULTI_STATUS as benign]]
- [[materialize-code-review]]
