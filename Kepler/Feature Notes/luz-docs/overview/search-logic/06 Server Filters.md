# 06 — Automatic Server Filters

## Why this matters

Even if your `query` says "return everything", luz-docs **silently appends** more conditions before hitting MongoDB. This is the safety net — it stops users seeing data they shouldn't, and stops half-built records from leaking into results.

If your search returns fewer results than you expected, these are usually why.

## The four automatic filters

| Filter | Condition added | Can it be bypassed? |
|--------|------------------|---------------------|
| **Deletion status** | `_deletionStatus = "false"` OR field absent | Yes — pass `include-deleted-records=true` |
| **Being-created guard** | `is_being_created` is absent or `false` | No — always applied |
| **Security class** | Document/folder must match caller's clearance | Yes (admin) — pass `skip-security-classes=true` |
| **Credential mapping** | Personal documents filtered by credential token | No — always applied |

All of these — plus your client query — are combined under one top-level `$and`. So in MongoDB the final filter looks like:

```
$and: [
  <your client query>,
  <deletion-status filter>,
  <being-created filter>,
  <security-class filter>,
  <credential filter>
]
```

## Each filter in plain English

### Deletion status
A document with `_deletionStatus: "true"` is [[Glossary#Soft delete|soft-deleted]] — hidden but not erased. The server hides it by default. Set `include-deleted-records=true` to see them (admin / audit use).

### Being-created guard
While a document is still being uploaded / processed, `is_being_created` is true. The server hides those because they're not "ready" yet. There's no way to opt out — partial documents would confuse users.

### Security class
Each document and folder has [[Glossary#Security class|security class]] codes. The caller's identity carries a list of codes they're allowed to see. The server filters out anything whose codes don't intersect with the caller's. Admins can pass `skip-security-classes=true` for support work.

### Credential mapping
Personal documents are double-protected: even within their security class, a personal document is only visible to the user it was [[Glossary#Credential mapping|credentialed]] to. There's no flag to bypass this — it would break user privacy.

---

**Navigation:** [[05 Operator Nesting|← prev]] · [[search-logic|↑ index]] · [[07 Aggregation Pipeline|next →]]
