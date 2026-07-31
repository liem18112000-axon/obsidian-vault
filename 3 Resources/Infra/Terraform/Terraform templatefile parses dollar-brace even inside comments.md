---
title: "Terraform templatefile parses dollar-brace even inside comments"
created: 2026-07-26
type: lesson
status: seedling
source: "session 2026-07-26"
tags: [terraform, templatefile, gotcha, cloud-init]
---

# Terraform templatefile parses dollar-brace even inside comments

Terraform's templatefile() interprets the ${ ... } interpolation token ANYWHERE in the file, including inside comment lines. A literal ${...} in a .tftpl (even documentation like "used for docker compose ${...} interpolation") is a parse error: "Invalid expression; Expected the start of an expression".

Fixes: escape as $${...} to emit a literal, or reword to avoid the token.

Corollary technique for cloud-init: files that legitimately contain literal ${VAR} (docker-compose interpolation, shell scripts) should be read with file() (verbatim, no parsing), NOT templatefile(). Then embed them into the cloud-config via write_files with `encoding: b64` and content = base64encode(...) — base64 sidesteps both the ${} parsing problem and YAML indentation pitfalls. Reserve templatefile() for files where you actually inject Terraform values and that contain no literal ${}.
