---
ai_hash: 8a3d0a2edc7380f9
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-10
entities: []
source: code-review session 2026-06-10, fb-info-project
status: seedling
tags:
- openpyxl
- excel
- float-precision
- gotcha
title: Excel rounds 16-17 digit ids stored as number cells before openpyxl reads them
type: lesson
---

# Excel rounds 16-17 digit ids stored as number cells before openpyxl reads them

Excel stores numbers as IEEE-754 doubles (~15 significant digits), so a 16-17 digit id (Facebook UID, snowflake id) typed into a NUMBER cell is rounded *by Excel itself* — `openpyxl` with `data_only=True` then faithfully returns the already-corrupted float (often in scientific notation like `1.0245019e+16`). No amount of parsing on the Python side can recover the lost digits.

Rule: long numeric ids must be stored as TEXT cells (or prefixed with `'`) in the workbook. Code reading such columns should still handle the float/scientific form (`str(int(float(v)))`) as a degraded fallback, but the real fix is upstream in the sheet.

%% ai-graph-start %%

**Related notes:**
- _(none above threshold)_

%% ai-graph-end %%