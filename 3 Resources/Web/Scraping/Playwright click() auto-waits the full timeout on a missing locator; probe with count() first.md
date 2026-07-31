---
ai_hash: 86170c1ee319e7ad
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-30
entities: []
source: fb-info-project thorough-mode debugging, session 2026-06-30
status: seedling
tags:
- playwright
- timeout
- performance
- gotcha
title: Playwright click() auto-waits the full timeout on a missing locator; probe
  with count() first
type: lesson
---

# Playwright click() auto-waits the full timeout on a missing locator; probe with count() first

Playwright actions **auto-wait**: `locator.click()` (and `scroll_into_view_if_needed`, `fill`, etc.) block until the element is attached + actionable, up to their timeout. So calling `click()` on a locator that matches **nothing** does not fail fast — it waits the full timeout (default 30s; in fb-info-project's `browser.click` it's scroll_into_view 3s + click 5s = ~8s) and only then throws.

**Gotcha:** an expand-until-exhausted loop that does `click(locator.first)` every round to test 'is there still a button?' pays that full timeout on every round once the buttons run out. Multiply by many rounds (and a thorough/retry mode that adds more rounds) and the loop silently burns minutes — looking exactly like a hang, with no log output if the empty rounds aren't logged. In fb-info-project this pushed the comment-expand phase past the 10-min page timeout with zero progress lines.

**Fix:** gate the click on presence first — `locator.count()` returns the current match count **immediately and never auto-waits**. `if count() and click(loc.first): ...` skips the expensive wait-on-missing-element. Cheap existence probe before an auto-waiting action.

## Related
[[Distinguish absent control from missed click when expanding lazy lists]]
[[innerText forces layout and can hang Playwright scans on huge DOMs; prefer textContent]]
[[fb-info-project]]

## Related

- [[Distinguish absent control from missed click when expanding lazy lists]]
- [[innerText forces layout and can hang Playwright scans on huge DOMs; prefer textContent]]

%% ai-graph-start %%

**Related notes:**
- [[innerText forces layout and can hang Playwright scans on huge DOMs; prefer textContent]]
- [[Distinguish absent control from missed click when expanding lazy lists]]
- [[Large FB group posts expand huge but article-scan yields almost no profiles]]
- [[Playwright browser_wait_for time is a hard sleep]]
- [[scrapling goto waits for load event + retries=3; on FB SPA that means ~90s per dead profile]]

%% ai-graph-end %%