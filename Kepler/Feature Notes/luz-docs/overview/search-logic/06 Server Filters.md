---
ai_hash: b3c0b814bbb2b2ac
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Server Filters
- Automatic Server Filters
- luz-docs
- MongoDB
- Deletion status
- Being-created guard
- Security class
- Credential mapping
- _deletionStatus
- is_being_created
- include-deleted-records=true
- skip-security-classes=true
- $and
- Soft delete
- Security class codes
- Personal documents
- Admin
- Caller
- User
- 05 Operator Nesting
- search-logic
- 07 Aggregation Pipeline
- Glossary
- document
- folder
- credential token
- user privacy
---

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

%% ai-graph-start %%

**Related notes:**
- [[search-logic]]
- [[Glossary]]
- [[04 Query Operators]]
- [[07 Aggregation Pipeline]]
- [[02 Endpoints]]

**Relations:**
- Automatic Server Filters — *are a type of* — Server Filters
- luz-docs — *appends conditions before hitting* — MongoDB
- Automatic Server Filters — *include* — Deletion status
- Automatic Server Filters — *include* — Being-created guard
- Automatic Server Filters — *include* — Security class
- Automatic Server Filters — *include* — Credential mapping
- Deletion status — *condition is* — _deletionStatus = "false" OR field absent
- Deletion status — *can be bypassed by* — include-deleted-records=true
- Being-created guard — *condition is* — is_being_created is absent or false
- Being-created guard — *cannot be bypassed* — true
- Security class — *condition is* — Document/folder must match caller's clearance
- Security class — *can be bypassed by* — skip-security-classes=true
- skip-security-classes=true — *is used by* — Admin
- Credential mapping — *condition is* — Personal documents filtered by credential token
- Credential mapping — *cannot be bypassed* — true
- Server Filters — *are combined under* — $and
- $and — *combines* — deletion-status filter
- $and — *combines* — being-created filter
- $and — *combines* — security-class filter
- $and — *combines* — credential filter
- document — *with _deletionStatus: "true" is* — Soft delete
- Soft delete — *is defined in* — Glossary
- include-deleted-records=true — *is for* — admin / audit use
- document — *while being uploaded/processed has* — is_being_created
- document — *has* — Security class codes
- folder — *has* — Security class codes
- Caller's identity — *carries* — list of codes
- Security class — *is defined in* — Glossary
- Personal documents — *are visible to* — user it was credentialed to
- Credential mapping — *is defined in* — Glossary
- Credential mapping — *protects* — user privacy
- 05 Operator Nesting — *precedes* — 06 Server Filters
- 06 Server Filters — *is part of* — search-logic
- 06 Server Filters — *precedes* — 07 Aggregation Pipeline

%% ai-graph-end %%