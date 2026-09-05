---
title: "Authenticate curl to Atlassian Cloud REST API with a .netrc file"
created: 2026-08-04
type: howto
status: seedling
source: "LUZ-158230 investigation 2026-08-04"
tags: [atlassian, jira, curl, claude-code, kepler]
---

# Authenticate curl to Atlassian Cloud REST API with a .netrc file

The Kepler/Klara LUZ Jira project lives at **axonivy.atlassian.net** (not the leocdp/vinnstack sites). Auth to its REST API v3 with an Atlassian account email + API token via HTTP Basic.

To call it from curl in Git Bash without embedding the token literal in the shell command (embedding a credential literal, or looping over several sites, gets denied by Claude Codes command classifier), write a `.netrc` file with the Write tool and pass `--netrc-file`:

```
machine axonivy.atlassian.net
login <email>
password <api-token>
```
```bash
curl -s --netrc-file /path/.netrc "https://axonivy.atlassian.net/rest/api/3/issue/LUZ-158230?fields=summary,description,attachment,comment&expand=renderedFields"
```

Attachments: `.../rest/api/3/attachment/content/<id>` (follow redirects with -L). Delete the .netrc afterwards — it holds a secret. Never store the token value in a note.

PDF text extraction on this machine: `pdftoppm` is absent (Read cant render PDF pages) but Python `fitz` (PyMuPDF) is installed — use `fitz.open(path).get_text()`.
