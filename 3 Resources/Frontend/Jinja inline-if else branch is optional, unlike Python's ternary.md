---
title: "Jinja inline-if else branch is optional, unlike Python's ternary"
created: 2026-06-14
type: lesson
status: seedling
source: "session 2026-06-14, Copilot PR review analysis"
tags: [jinja2, templating, python, code-review, gotcha]
---

# Jinja inline-if else branch is optional, unlike Python's ternary

**In Jinja2 the inline conditional expression's `else` branch is OPTIONAL — `{{ a if b }}` is valid and evaluates to `undefined` (renders as an empty string by default) when the condition is false.** This is unlike Python, where a ternary `a if b else c` requires `else`.

So a template like `{{ 'checked' if config and config.enabled }}` compiles and renders fine: 'checked' when true, '' when false. It does NOT raise TemplateSyntaxError.

This is a common false-positive from reviewers/linters (incl. GitHub Copilot, observed 2026-06) that apply Python ternary rules to Jinja. When a tool flags 'missing else' in a Jinja `{{ x if cond }}`, verify by actually rendering it before 'fixing' — the rendered output (and any test that renders the page) is the ground truth.

A gotcha that IS real in the same spot: `{{ value or '' }}` blanks a legitimate falsy value like `0`/`0.0`. For a numeric field that can legitimately be 0, use `{{ value if value is not none else '' }}` instead of `value or ''`.

Verified by rendering with `jinja2.Environment().from_string(...).render(...)` and by a passing page-render test.
