---
ai_hash: 180cc3285b111a62
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-22
entities: []
source: fb-info-project session 2026-06-22
status: seedling
tags:
- facebook
- scraping
- regex
- gotcha
title: Unanchored 'From' regex captures the profile name from Facebook's 'See more
  from' buttons
type: lesson
---

# Unanchored 'From' regex captures the profile name from Facebook's 'See more from' buttons

When parsing a Facebook profile's visible body text for the hometown intro field, an **unanchored, case-insensitive `From` regex is a trap**: Facebook's feed is full of **"See more from `<Name>`"** buttons, so `(?:From)\s+(.{2,60})` matches that "from `<Name>`" and captures the profile's **own name** as the hometown. Every profile lacking a public hometown then gets its own name in the "Quê quán" column.

**Fix:** anchor the intro label to the start of its line — `(?m)^\s*(?:Đến từ|From)\s+(.{2,60})$` — because the real intro ("From `<city>`" / "Đến từ `<city>`") sits at the start of its own line, while "See more from X" has "from" mid-line.

General lesson: regexes over scraped UI text must anchor on label position, not just the label word — common English words ("From", "Lives", "At") recur in chrome/buttons. The Vietnamese label "Đến từ" was safe (the VN "see more from" is "Xem thêm **từ**", a different token), which is why only English profiles showed the bug.

Context: fb-info-project `src/patterns.py` HOME regex, branch `fix/hometown-see-more-from`. Related: [[Facebook reply hierarchy lives in the article aria-label, not DOM nesting]].

## Related

- [[3 Resources/Web/Scraping/Facebook/Facebook reply hierarchy lives in the article aria-label, not DOM nesting]]

%% ai-graph-start %%

**Related notes:**
- [[Verify the Quê quán hometown fix by asserting no output row has Quê quán equal to Name]]
- [[Facebook reply-expander button label variants]]
- [[Facebook ships comment aria-labels in English even when the UI is Vietnamese]]
- [[Facebook reply hierarchy lives in the article aria-label, not DOM nesting]]
- [[Facebook's Newest sort option mentions 'all comments' in its description — anchor the label regex]]

%% ai-graph-end %%