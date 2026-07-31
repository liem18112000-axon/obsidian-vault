---
ai_hash: 0a484df293b9583e
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-26
entities: []
source: session 2026-07-26
status: seedling
tags:
- terraform
- templatefile
- gotcha
- cloud-init
title: Terraform templatefile parses dollar-brace even inside comments
type: lesson
---

# Terraform templatefile parses dollar-brace even inside comments

Terraform's templatefile() interprets the ${ ... } interpolation token ANYWHERE in the file, including inside comment lines. A literal ${...} in a .tftpl (even documentation like "used for docker compose ${...} interpolation") is a parse error: "Invalid expression; Expected the start of an expression".

Fixes: escape as $${...} to emit a literal, or reword to avoid the token.

Corollary technique for cloud-init: files that legitimately contain literal ${VAR} (docker-compose interpolation, shell scripts) should be read with file() (verbatim, no parsing), NOT templatefile(). Then embed them into the cloud-config via write_files with `encoding: b64` and content = base64encode(...) — base64 sidesteps both the ${} parsing problem and YAML indentation pitfalls. Reserve templatefile() for files where you actually inject Terraform values and that contain no literal ${}.

%% ai-graph-start %%

**Related notes:**
- [[Cloud Build treats $VAR in step args as its own substitution; escape shell $ as $$]]

%% ai-graph-end %%