---
ai_hash: 53c7bcac4a8c4983
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-04
entities:
- _folderNames
- folder
- document
- folderId
- document.folderIds
- name
- ancestor folder
- parentFolderIds
- descendant docs
- _folderSecurityClassCodes
- pipeline stage
- stagePerFolderNamesAndCodes
- folder.securityClassCodes
- folder.inheritedSecurityClassCodes
- security-code union
- parent-id update
- parent-change cascade
- MaterializeComputeBuilder.stagePerFolderNamesAndCodes
- $map
- $setUnion
- Folder parent-change cascade is a no-op when the security-code union is unchanged
- guard
- codes-side
source: LUZ-154159 cascade pipeline read
status: seedling
tags:
- luz-docs
- materialize
- cascade
- mongodb
title: _folderNames is parent-chain-independent — depends only on each folder's own
  name
type: observation
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

%% ai-graph-start %%

**Related notes:**
- [[Folder parent-change cascade is a no-op when the security-code union is unchanged]]
- [[luz_docs parent-change cascade pipeline rebuilds _folderSecurityClassCodes positionally then re-derives the sentinels]]
- [[04 Materialized Fields Computation]]
- [[Materialize folder parentFolderIds change cascade (LUZ-154159)]]
- [[Missing folder reference produces fail-closed materialize state]]

**Relations:**
- _folderNames — *is* — parent-chain-independent
- _folderNames — *depends only on* — folder.name
- _folderNames — *is a field on* — document
- _folderNames — *is a list of* — names
- _folderNames — *has one entry per* — folderId
- folderId — *is in* — document.folderIds
- ancestor folder — *has* — parentFolderIds
- parentFolderIds — *change does not affect* — _folderNames on descendant docs
- _folderSecurityClassCodes — *is a* — per-folder array
- _folderSecurityClassCodes — *is in the same pipeline stage as* — _folderNames
- _folderSecurityClassCodes — *is processed in* — stagePerFolderNamesAndCodes
- _folderNames — *is processed in* — stagePerFolderNamesAndCodes
- _folderSecurityClassCodes — *depends on* — parent chain
- _folderSecurityClassCodes — *is derived from* — folder.securityClassCodes
- _folderSecurityClassCodes — *is derived from* — folder.inheritedSecurityClassCodes
- folder.inheritedSecurityClassCodes — *walks* — ancestors
- security-code union — *is unchanged across* — parent-id update
- guard — *proves* — security-code union is unchanged
- guard — *is sound for skipping* — parent-change cascade
- parent-change cascade — *rewrites* — _folderNames
- MaterializeComputeBuilder.stagePerFolderNamesAndCodes — *computes* — _folderNames
- MaterializeComputeBuilder.stagePerFolderNamesAndCodes — *computes* — _folderSecurityClassCodes
- _folderNames — *uses* — $map
- _folderNames — *takes* — folder.name
- _folderSecurityClassCodes — *uses* — $map
- _folderSecurityClassCodes — *uses* — $setUnion
- $setUnion — *combines* — folder.securityClassCodes
- $setUnion — *combines* — folder.inheritedSecurityClassCodes
- codes-side — *reads* — inherited
- Folder parent-change cascade is a no-op when the security-code union is unchanged — *is related to* — parent-change cascade
- Folder parent-change cascade is a no-op when the security-code union is unchanged — *is related to* — security-code union

%% ai-graph-end %%