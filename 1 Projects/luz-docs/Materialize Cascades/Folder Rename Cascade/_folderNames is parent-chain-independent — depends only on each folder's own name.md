---
title: "_folderNames is parent-chain-independent — depends only on each folder's own name"
created: 2026-06-04
type: observation
status: seedling
source: "LUZ-154159 cascade pipeline read"
tags: [luz-docs, materialize, cascade, mongodb]
---

# _folderNames is parent-chain-independent — depends only on each folder's own name

The materialized \`_folderNames\` field on a document is the list of names of the folders the doc *directly* belongs to (one entry per \`folderId\` in \`document.folderIds\`). Each entry is just that folder's own \`name\` — there's no walk up the parent chain. So when an ancestor folder's \`parentFolderIds\` changes, \`_folderNames\` on descendant docs **does not change**.

This is non-obvious because \`_folderSecurityClassCodes\` — also a per-folder array in the same pipeline stage (\`stagePerFolderNamesAndCodes\`) — DOES depend on the parent chain (it's \`folder.securityClassCodes ∪ folder.inheritedSecurityClassCodes\`, and \`inheritedSecurityClassCodes\` walks ancestors). Easy to assume both fields share the same dependency, but they don't.

## Practical consequence

A guard that proves the *security-code union is unchanged* across a parent-id update is sound for skipping the parent-change cascade entirely, even though the cascade pipeline also rewrites \`_folderNames\`. See [[Folder parent-change cascade is a no-op when the security-code union is unchanged]].

## Where to look

\`MaterializeComputeBuilder.stagePerFolderNamesAndCodes\`:
- \`_folderNames\` ← \`\$map\` over each looked-up folder, takes \`folder.name\` directly.
- \`_folderSecurityClassCodes\` ← \`\$map\` over the same lookups, takes \`\$setUnion\` of \`folder.securityClassCodes\` and \`folder.inheritedSecurityClassCode\`.

The asymmetry is the whole reason: only the codes-side reads inherited.

## Related

- [[Folder parent-change cascade is a no-op when the security-code union is unchanged]]
