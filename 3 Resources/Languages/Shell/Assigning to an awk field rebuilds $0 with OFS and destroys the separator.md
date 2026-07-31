---
title: "Assigning to an awk field rebuilds $0 with OFS and destroys the separator"
created: 2026-07-31
type: gotcha
status: seedling
source: "vault PARA reorganization 2026-07-31"
tags: [awk, shell, text-processing, gotcha]
---

# Assigning to an awk field rebuilds $0 with OFS and destroys the separator

In awk, assigning to any field — including an assignment that changes nothing — forces awk to **rebuild `$0` by joining the fields with `OFS`** (a single space by default). The original separator text is gone.

This bit me while sorting rule lines of the form `src => dst` by path depth:

```awk
# BROKEN: gsub() assigns to $1, so $0 is rebuilt and " => " becomes " "
awk -F' => ' '{n=gsub(/\//,"/",$1); print n"\t"$0}'
```

Every downstream `${line%% => *}` split then failed, and 48 rules silently fell through to their parent rule — sending files to the wrong destination.

Fix: copy the field into a plain variable and modify *that*, leaving `$0` untouched.

```awk
awk -F' => ' '{s=$1; n=gsub(/\//,"/",s); print n"\t"$0}'
```

The same trap applies to `sub()`, `gsub()`, and any `$1 = ...`. If you only need to *read* a field, never assign to it. If you genuinely want a rebuild, set `OFS` first.

## Related

- [[Precompute and validate a file-level move map before a mass reorganization]]
