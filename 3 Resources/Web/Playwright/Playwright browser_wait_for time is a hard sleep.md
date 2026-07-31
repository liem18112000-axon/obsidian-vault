---
ai_hash: 29854c3cccdb5ebf
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-18
entities: []
tags:
- playwright
- mcp
- gotcha
- testing
---

# Playwright MCP `browser_wait_for {time}` is a hard sleep

The `time` arg on `mcp__playwright__browser_wait_for` is **not** a max-timeout — it unconditionally sleeps that many seconds *before* checking, then waits for the text. Generated code:

```js
await new Promise(f => setTimeout(f, 40 * 1000)); // ALWAYS sleeps 40s
await page.getByText("Manage access rights").first().waitFor({ state: 'visible' });
```

**Consequence:** any wall-clock load timing bracketed around `wait_for {time:40}` is garbage — always ≈40s. 

**Fix:** omit `time` (or use tiny value) and rely on the text predicate for the wait; get real timings from in-page `performance.now()` marks instead of shell timestamps around the call.

Related: [[Measure component render timing with Playwright addInitScript]]

%% ai-graph-start %%

**Related notes:**
- [[Playwright click() auto-waits the full timeout on a missing locator; probe with count() first]]
- [[Measure component render timing with Playwright addInitScript]]
- [[Playwright full-nav detection needs a JS-heap marker not URL compare]]
- [[scrapling goto waits for load event + retries=3; on FB SPA that means ~90s per dead profile]]
- [[Reload not automatically faster than first load]]

%% ai-graph-end %%