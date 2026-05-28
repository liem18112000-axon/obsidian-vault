# 04 — Query Operators

The `query` field in the [[03 Request Body|request body]] is a JSON object using one of these operators. Each operator is a building block.

All operators are defined in `JsonStoreQueryExpression.java`. The conversion to MongoDB syntax happens in `JsonStoreSearchQueryUtil.java`.

> **Mental model:** the client sends a JSON tree. luz-docs walks the tree, converts each node to its MongoDB equivalent, and glues the pieces together into one big MongoDB query.

## Operator index

| Operator | One-liner | Section |
|----------|-----------|---------|
| `term` | Exact match on one value | [[#term — Exact match]] |
| `terms` | Match any value in a list | [[#terms — Match any of multiple values]] |
| `exists` | Field is present and not empty | [[#exists — Field has a value]] |
| `not` | Invert another operator | [[#not — Negate a sub-query]] |
| `regexp` | Pattern match on text | [[#regexp — Regular expression match]] |
| `range` | Numeric/date comparisons | [[#range — Numeric or date comparison]] |
| `and` | All sub-queries must match | [[#and — Logical AND]] |
| `or` | At least one sub-query must match | [[#or — Logical OR]] |

---

## term — Exact match

Matches documents where a field equals exactly one given value. Case-sensitive — `"Mobiliar"` ≠ `"mobiliar"`.

### Plain-English meaning
"The field has *this exact* value, character for character."

### Client syntax

```json
{ "term": { "fieldName": "value" } }
```

### MongoDB output

```json
{ "fieldName": "value" }
```

**Implementation:** `JsonStoreSearchQueryUtil.handleQueryTerm()` — [[Glossary#Pass-through (in code)|pass-through]] with no transformation.

---

## terms — Match any of multiple values

Matches if a field equals **any** value in a list. Unlike `term`, this one is **case-insensitive** — values are matched as [[Glossary#Regex (Regular Expression)|regex]]es with the `i` option.

### Plain-English meaning
"The field's value is in this set: A, B, or C — and case doesn't matter."

### Client syntax

```json
{ "terms": { "fieldName": ["value1", "value2"] } }
```

### MongoDB output

```json
{
  "$or": [
    { "fieldName": { "$regex": "^value1$", "$options": "i" } },
    { "fieldName": { "$regex": "^value2$", "$options": "i" } }
  ]
}
```

### Special case — searching by `_id`

Values are validated as 24-character hex strings (must look like a real [[Glossary#ObjectId|ObjectId]]), then compared via `$toObjectId` + `$expr/$eq`. Garbage `_id`s are rejected before they reach MongoDB.

**Implementation:** `JsonStoreSearchQueryUtil.handleQueryTerms()`

---

## exists — Field has a value

Two flavours: regular field vs. [[Glossary#Array (in MongoDB)|array]] field.

### Regular field
"The field exists on this document AND is not an empty string."

```json
{ "exists": { "field": "fieldName" } }
```

MongoDB:
```json
{ "fieldName": { "$exists": true, "$ne": "" } }
```

### Array field
"The array exists AND has at least one element." Use `"type": "array"`.

```json
{ "exists": { "field": "folderIds", "type": "array" } }
```

MongoDB:
```json
{ "folderIds.0": { "$exists": true } }
```

> Why `folderIds.0`? It checks whether index 0 exists — which is true if and only if the array has at least one element.

**Implementation:** `JsonStoreSearchQueryUtil.handleQueryExists()` → `buildExistsField()` / `buildExistsArrayField()`

---

## not — Negate a sub-query

Wraps any other operator and inverts the match. Negation is supported for: `term`, `terms`, `exists`, `regexp`, `range`. (Not `and`/`or` — you'd typically restructure those instead.)

### Plain-English meaning
"Find documents that do **not** match the wrapped condition."

### Client syntax

```json
{ "not": { "<operator>": { ... } } }
```

### Worked examples

**Not exists (regular field)** — field is absent or empty:
```json
{ "not": { "exists": { "field": "parentFolderIds" } } }
```
MongoDB:
```json
{ "$or": [ { "parentFolderIds": { "$exists": false } }, { "parentFolderIds": "" } ] }
```

**Not exists (array field)** — array missing or empty:
```json
{ "not": { "exists": { "field": "folderIds", "type": "array" } } }
```
MongoDB:
```json
{ "$or": [ { "folderIds": { "$exists": false } }, { "folderIds": { "$size": 0 } } ] }
```

**Not term:**
```json
{ "not": { "term": { "status": "deleted" } } }
```
MongoDB: `{ "status": { "$not": { "$regex": "^deleted$", "$options": "i" } } }`

**Not terms:**
```json
{ "not": { "terms": { "status": ["a", "b"] } } }
```
MongoDB:
```json
{ "$and": [
  { "status": { "$not": { "$regex": "^a$", "$options": "i" } } },
  { "status": { "$not": { "$regex": "^b$", "$options": "i" } } }
] }
```

**Not regexp:**
```json
{ "not": { "regexp": { "documentTitle": ".*invoice.*" } } }
```
MongoDB: `{ "documentTitle": { "$not": { "$regex": ".*invoice.*", "$options": "i" } } }`

**Not range:**
```json
{ "not": { "range": { "_createdDate": { "gte": "2024-01-01", "lte": "2024-12-31" } } } }
```
MongoDB: `{ "$expr": { "$not": { "$and": [ { "$gte": [ ... ] }, { "$lte": [ ... ] } ] } } }`

**Implementation:** `JsonStoreSearchQueryUtil.handleQueryNot()` → `buildNotQueryBaseOnExpression()`

---

## regexp — Regular expression match

Match text by [[Glossary#Regex (Regular Expression)|pattern]]. Always [[Glossary#Case-insensitive|case-insensitive]].

### Plain-English meaning
"The field's text matches this pattern." Use it for substring search ("contains 'invoice'") or fuzzy matches.

### Client syntax

```json
{ "regexp": { "fieldName": "pattern" } }
```

### Pattern rules

| Client pattern | What it means | MongoDB regex generated |
|----------------|---------------|-------------------------|
| `".*value.*"` | Substring match (starts and ends with `.*`) | `.*<escaped-value>.*` |
| `"exactValue"` | Exact match | `^<escaped-value>$` |

luz-docs **escapes** special regex characters automatically so users can't accidentally (or maliciously) break the pattern: `` { } ( ) [ ] . + * ? ^ $ \ | ``

### Worked examples

**Substring search:**
```json
{ "regexp": { "documentTitle": ".*Post AG.*" } }
```
MongoDB: `{ "documentTitle": { "$regex": ".*Post\\ AG.*", "$options": "i" } }`

**Exact match:**
```json
{ "regexp": { "status": "active" } }
```
MongoDB: `{ "status": { "$regex": "^active$", "$options": "i" } }`

> **Gotcha:** the `".*value.*"` form must be at least 5 characters total (the `.*` wrappers count). Shorter patterns → [[Glossary#HTTP 400 / HTTP status codes|HTTP 400]]. This prevents wide-open searches like `.*a.*` that would scan the entire collection.

**Implementation:** `JsonStoreSearchQueryUtil.handleQueryRegexp()` / `getRegexSearch()` / `escapeSpecialRegexChars()`

---

## range — Numeric or date comparison

Compare a numeric or date field against bounds. Uses MongoDB [[Glossary#`$expr`|`$expr`]] with typed comparisons; date strings are auto-parsed via `$dateFromString`.

### Plain-English meaning
"This field's value is between X and Y" (any of greater-than, less-than, or equal).

### Client syntax

```json
{
  "range": {
    "fieldName": { "gte": <value>, "lte": <value>, "gt": <value>, "lt": <value> }
  }
}
```

### Supported comparisons

| Client key | MongoDB operator | Meaning |
|------------|------------------|---------|
| `gte` | `$gte` | Greater than or equal |
| `lte` | `$lte` | Less than or equal |
| `gt`  | `$gt`  | Greater than |
| `lt`  | `$lt`  | Less than |

Any combination can be used together. E.g. `{ "gte": 10, "lt": 100 }` = "from 10 (inclusive) up to but not including 100".

### Worked example — date range

```json
{
  "range": {
    "_createdDate": {
      "gte": "2021-10-11T09:32:51.099Z",
      "lte": "2021-10-20T09:35:23.513Z"
    }
  }
}
```

MongoDB:
```json
{
  "$expr": {
    "$and": [
      { "$gte": [ { "$dateFromString": { "dateString": "$_createdDate", "onError": null } },
                  { "$dateFromString": { "dateString": "2021-10-11T09:32:51.099Z", "onError": null } } ] },
      { "$lte": [ { "$dateFromString": { "dateString": "$_createdDate", "onError": null } },
                  { "$dateFromString": { "dateString": "2021-10-20T09:35:23.513Z", "onError": null } } ] }
    ]
  }
}
```

`$dateFromString` parses the stored string into a real Date so the comparison is chronological, not alphabetical.

### Worked example — numeric range

```json
{ "range": { "_files.reference._sizeInBytes": { "gte": 1, "lte": 52428800 } } }
```

MongoDB (no `$dateFromString` needed — it's already a number):
```json
{
  "$expr": {
    "$and": [
      { "$gte": [ "$_files.reference._sizeInBytes", 1 ] },
      { "$lte": [ "$_files.reference._sizeInBytes", 52428800 ] }
    ]
  }
}
```

**Implementation:** `JsonStoreSearchQueryUtil.handleQueryRange()` / `buildComparisonOperatorQuery()`

---

## and — Logical AND

Match if **all** sub-queries match.

```json
{ "and": [ <query1>, <query2>, ... ] }
```

MongoDB: `{ "$and": [ ... ] }`

### Example
```json
{
  "and": [
    { "exists": { "field": "user.id" } },
    { "exists": { "field": "user.email" } }
  ]
}
```
= "documents where both `user.id` and `user.email` exist."

**Implementation:** `JsonStoreSearchQueryUtil.buildOperatorQuery(JsonStoreOperators.AND, ...)`

---

## or — Logical OR

Match if **at least one** sub-query matches.

```json
{ "or": [ <query1>, <query2>, ... ] }
```

MongoDB: `{ "$or": [ ... ] }`

### Example — fulltext search across multiple fields
```json
{
  "or": [
    { "regexp": { "documentTitle": ".*Post.*" } },
    { "regexp": { "description": ".*Post.*" } },
    { "regexp": { "senderName": ".*Post.*" } },
    { "regexp": { "documentTextContent": ".*Post.*" } }
  ]
}
```
= "the word 'Post' appears in any of these fields."

**Implementation:** `JsonStoreSearchQueryUtil.buildOperatorQuery(JsonStoreOperators.OR, ...)`

---

**Navigation:** [[03 Request Body|← prev]] · [[search-logic|↑ index]] · [[05 Operator Nesting|next →]]
