---
title: "Identity resolution needs confidence gating and identifier normalization"
created: 2026-09-05
type: lesson
status: seedling
source: "c360 python code review 2026-09-05"
tags: [identity-resolution, cdp, data-quality, matching]
---

# Identity resolution needs confidence gating and identifier normalization

An identity-resolution matcher that ORs every available identifier (including weak signals like cookie_id / device_id / advertising_id) and links on any single match over-merges distinct people. A shared kiosk cookie or a reissued cookie then fuses two real people into one golden record, irreversibly.

Two compounding defects to avoid:
- Confidence = matches / (number of conditions present) makes a lone weak match score 1.0, so the score cannot gate merges.
- Comparing email/phone without normalization (lowercase, trim, E.164) fragments the same person into duplicate masters, which weak signals later re-bridge.

Do instead: score by identifier *strength*, require a minimum confidence to auto-merge (route weak-only matches to review), normalize identifiers before compare, and add deterministic tie-breaks plus transitive (union-find) merge of masters.
