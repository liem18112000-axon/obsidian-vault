---
title: "Extracting every link from Jira ADF and Confluence storage"
created: 2026-08-27
type: howto
status: seedling
source: "session 2026-08-27 — kga extract.py"
tags: [atlassian, adf, confluence, jira, link-extraction]
---

# Extracting every link from Jira ADF and Confluence storage

To reliably pull **every** link from Atlassian content, use three sources and dedup by a canonical key:

**Jira ADF (Atlassian Document Format)** — a JSON tree. Links live in two shapes:
- text nodes with a `link` **mark**: `node.marks[].type=="link"` → `attrs.href` (anchor = the node text).
- **smart-links** as their own nodes: `type in {inlineCard, blockCard, embedCard}` → `attrs.url` (no anchor).
Recurse `node["content"]`. A URL pasted as **plain text** has NO link mark — you miss it unless you also regex the concatenated ADF text. That is the classic gap.

**Confluence storage format** — XHTML. Anchors are `<a href>`; external macros are `<ri:url ri:value="...">`. BeautifulSoup(`html.parser`) parses namespaced tags, but a robust cheap trick is: collect `a[href]` via the parser AND regex the whole storage string for `https?://` (catches `ri:url` values + raw URLs). Watch for xmlns noise on real bodies.

**Classify + dedup:** map each URL to a `(type, canonical)` — `jira:<KEY>` from `/browse/`, `confluence:<id>` from `/wiki/.../pages/<id>`, else host-based (bitbucket/figma/google-doc/attachment/external). Dedup by canonical so the same page found via description + remotelink collapses to one record. Issue links and child pages give the KEY/id directly — set their canonical explicitly, do not URL-classify. Mark `in_scope = type in follow_types` (follow) vs record-only.

Context: kga `extract.py` (LUZ-159671 test-agent).

## Related

- [[Knowledge-Gathering loop is a bounded frontier crawl with a verify edge]]
