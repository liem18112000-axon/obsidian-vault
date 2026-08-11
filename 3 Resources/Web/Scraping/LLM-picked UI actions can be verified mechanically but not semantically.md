---
ai_hash: 55fa20b44a3c2abd
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-11
entities: []
source: fb-info-project session 2026-06-11
status: evergreen
tags:
- llm
- verification
- scraping
- testing
- design
title: LLM-picked UI actions can be verified mechanically but not semantically
type: lesson
---

# LLM-picked UI actions can be verified mechanically but not semantically

When an LLM chooses which UI element to act on (self-healing selectors), you can verify the action **mechanically** — the click landed, the widget's label changed to the chosen item's title — but you cannot deterministically verify it **semantically** (that the chosen item *means* what you wanted): if you had a reliable semantic oracle, you wouldn't have needed the LLM.

A test that asserted 'verification rejects a semantically wrong pick' failed and exposed this. Workable guards instead of impossible verification:
1. **Regex-first within the candidate set** — if any candidate still matches the known-good pattern, take it and skip the model entirely.
2. **Known-wrong rejection** — refuse any pick that matches patterns for things you *know* are wrong (e.g. other sort modes like 'Newest').
3. **Scope the model to the failure path** — it only runs when the deterministic path already failed, so a wrong pick can't make things worse than the status quo.
4. Cache only after the mechanical verification passed, so a refused pick never persists.

## Related

- [[3 Resources/Web/Scraping/Self-healing scraper selectors — LLM fallback only on verified failure, then cache]]
- [[Facebook's Newest sort option mentions 'all comments' in its description — anchor the label regex]]

%% ai-graph-start %%

**Related notes:**
- [[Self-healing scraper selectors — LLM fallback only on verified failure, then cache]]
- [[Build test fakes from verbatim production data, decoys included]]
- [[Facebook's Newest sort option mentions 'all comments' in its description — anchor the label regex]]
- [[Verify Facebook comment sort switch by re-reading the sort button label]]
- [[Find then adversarial-refute verify pass cuts AI reviewer false positives]]

%% ai-graph-end %%