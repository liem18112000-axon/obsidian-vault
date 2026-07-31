---
title: "Resolving a wikilink by basename truncates titles containing a slash"
created: 2026-07-31
type: gotcha
status: seedling
source: "vault PARA reorganization 2026-07-31"
tags: [obsidian, wikilinks, tooling, gotcha]
---

# Resolving a wikilink by basename truncates titles containing a slash

When writing a link checker, the natural fallback for a `[[Some Note]]` target is "match the last path segment against every note basename". That fallback is wrong whenever the **link target itself contains a slash**, because `os.path.basename()` chops the title at that slash.

The target `[[Facebook /share/v/ links can resolve to reels]]` reduces to `" links can resolve to reels"`, which matches nothing — or worse, matches the wrong note.

The reason slashes appear inside titles at all: Obsidian (and note-creation scripts) strip `/` and other illegal characters from the **filename**, while the human-written link and the `title:` frontmatter keep them. So the on-disk file is `Facebook sharev links can resolve to reels.md` and the link says `Facebook /share/v/ links...`. They are the same note.

Fix: match on the **whole target string, normalized** — lowercase and strip every non-alphanumeric character — against the same normalization of each note basename.

```python
def norm(s): return re.sub(r'[^a-z0-9]', '', s.lower())
```

Only accept a normalized hit when it is unique. This one change repaired 122 links in this vault, covering titles mangled by `/` (`profile/list`, `event/list`, `Eclipse/Ivy`) and by `>` (`count>N`).

## Related

- [[Comma-split wikilinks leave dead fragment links in Related blocks]]
- [[Obsidian plugin configs pin folders to exact vault-root paths]]
