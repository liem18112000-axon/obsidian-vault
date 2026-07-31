---
title: "YAML parses a workflow's on: key as boolean True (Norway problem)"
created: 2026-06-06
type: lesson
status: seedling
source: "leo-cdp-framework ci-cd.yml 2026-06-06"
tags: [yaml, github-actions, parsing, gotcha]
---

# YAML parses a workflow's on: key as boolean True (Norway problem)

A GitHub Actions workflows top-level **`on:`** key is parsed by YAML 1.1 loaders (PyYAML `safe_load`, Ruby Psych, etc.) as the **boolean `True`**, not the string `"on"`. This is the "Norway problem" family: YAML 1.1 treats `on/off/yes/no/true/false` (any case) as booleans.

Consequence when scripting over a workflow file: `yaml.safe_load(f)["on"]` raises **`KeyError: on`** — the key is actually `True`. Access it as `d[True]`, or `d.get("on") or d.get(True)`. GitHub Actions itself parses the file correctly, so this is purely a gotcha for tools/scripts that load the YAML.

Same trap bites unquoted values: a country code `NO`, a port `ON`, or `version: 1.0` (float) silently change type. Quote them if you need the literal string.

## Related
- [[GitHub Copilot code review is a native PR reviewer, not a workflow job]]

## Related

- [[3 Resources/Infra/CI-CD/GitHub Actions/GitHub Copilot code review is a native PR reviewer, not a workflow job]]
