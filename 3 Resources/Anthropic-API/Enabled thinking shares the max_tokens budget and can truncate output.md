---
title: "Enabled thinking shares the max_tokens budget and can truncate output"
created: 2026-08-28
type: lesson
status: seedling
source: "session 2026-08-28 (KGA refine fix)"
tags: [anthropic, claude, llm, max-tokens, gotcha]
---

# Enabled thinking shares the max_tokens budget and can truncate output

When extended/adaptive thinking is enabled on a Claude request, **thinking tokens count against `max_tokens`** — `max_tokens` is a hard cap on thinking **plus** the visible response text, not just the answer. On a tight budget the model can spend most of it thinking and then truncate the actual output (or hit `stop_reason: "max_tokens"`).

This bites code written **before** thinking existed: short generation calls with small caps (e.g. `max_tokens=700` for a summary, `1500` for a JSON payload) were sized assuming the whole budget was output. After a model upgrade where thinking is on by default (Sonnet 5 / Opus 5 when `thinking` is omitted), the same call can return truncated or empty output.

**Fix:** for tight-budget generation calls either pass `thinking={"type": "disabled"}` to reclaim the full budget for output, or raise `max_tokens` to leave room for thinking. Relates to [[Anthropic SDK message content[0].text breaks on Claude 5 thinking blocks]].

## Related

- [[Anthropic SDK message content[0].text breaks on Claude 5 thinking blocks]]
