---
title: "HTMX hx-trigger=load auto-runs a form's default search on page load"
created: 2026-06-14
type: howto
status: seedling
source: "Accesstrade integration, session 2026-06-14"
tags: [htmx, frontend, web, pattern]
---

# HTMX hx-trigger=load auto-runs a form's default search on page load

To make an HTMX form auto-run its **default** request as soon as the page loads (instead of waiting for the user to click Submit), add the synthetic **`load`** event to its trigger list:

```html
<form hx-post="/ui/discover" hx-target="#results" hx-trigger="load, submit">
```

Key points:
- A form normally triggers on `submit`. Setting `hx-trigger` **replaces** the default, so you must list `submit` explicitly alongside `load` to keep the button working.
- On `load`, HTMX serializes the forms current inputs and fires the request once — so pre-fill sensible defaults via `value="..."` (rendered server-side) and the first request uses them.
- **Conditionally** auto-fire by emitting the attribute only when a default exists: `{% if default_campaign %}hx-trigger="load, submit"{% endif %}`. With no attribute, the form falls back to submit-only — avoids firing a request that needs a required field you cannot default (e.g. a campaign that is not cached yet).
- Beware auto-firing endpoints that hit a rate-limited or paid external API / background job on every page visit — that cost now happens on each load.

Related: [[Bound ThreadPoolExecutor + budget keeps per-item LLM scoring inside a web request window]].
