---
type: technical-section
topic: luz-docs search logic
level: beginner
---

# 03 Query Operators

Parent: [[Kepler/Feature Notes/luz-docs/overview/search-logic.md]]

Related:
- [[02 Endpoints and Request Body]]
- [[04 Operator Nesting]]
- [[08 Beginner Technical Terms]]

---

## What is a query operator?

A **query operator** is a keyword that tells the backend what kind of matching to perform.

Examples:

```text
term    -> exact match
terms   -> match any of many values
exists  -> field exists
range   -> value is between bounds
and     -> all conditions must match
or      -> at least one condition must match
not     -> invert a condition
regexp  -> text pattern match
```

All operators are defined in:

```text
JsonStoreQueryExpression.java
```

Conversion to MongoDB is performed by:

```text
JsonStoreSearchQueryUtil.java
```

---

## `term` — exact match

Use `term` when a field must equal one exact value.

Client syntax:

```json
{
  "term": {
    "senderName": "mobiliar"
  }
}
```

MongoDB output:

```json
{
  "senderName": "mobiliar"
}
```

Beginner meaning:

> Find documents where `senderName` is exactly `mobiliar`.

Implementation:

```text
JsonStoreSearchQueryUtil.handleQueryTerm()
```

---

## `terms` — match any value from a list

Use `terms` when a field can match one of several values.

Client syntax:

```json
{
  "terms": {
    "status": ["active", "pending"]
  }
}
```

MongoDB output shape:

```json
{
  "$or": [
    { "status": { "$regex": "^active$", "$options": "i" } },
    { "status": { "$regex": "^pending$", "$options": "i" } }
  ]
}
```

Beginner meaning:

> Match status `active` OR status `pending`, case-insensitively.

Special case for `_id`:

- values must be valid 24-character hex strings;
- comparison uses ObjectId conversion via `$toObjectId` and `$expr/$eq`.

Implementation:

```text
JsonStoreSearchQueryUtil.handleQueryTerms()
```

---

## `exists` — field has a value

Use `exists` when you want documents where a field is present.

Regular field syntax:

```json
{
  "exists": {
    "field": "senderName"
  }
}
```

MongoDB output:

```json
{
  "senderName": {
    "$exists": true,
    "$ne": ""
  }
}
```

Meaning:

> `senderName` exists and is not an empty string.

Array field syntax:

```json
{
  "exists": {
    "field": "folderIds",
    "type": "array"
  }
}
```

MongoDB output:

```json
{
  "folderIds.0": {
    "$exists": true
  }
}
```

Meaning:

> The array has at least one element.

Implementation:

```text
JsonStoreSearchQueryUtil.handleQueryExists()
JsonStoreQuerySearchUtil.buildExistsField()
JsonStoreQuerySearchUtil.buildExistsArrayField()
```

---

## `not` — negate a condition

Use `not` to invert another operator.

Client syntax:

```json
{
  "not": {
    "term": {
      "status": "deleted"
    }
  }
}
```

Meaning:

> Find documents where status is not deleted.

Supported inner operators:

- `term`
- `terms`
- `exists`
- `regexp`
- `range`

Implementation:

```text
JsonStoreSearchQueryUtil.handleQueryNot()
buildNotQueryBaseOnExpression()
```

---

## `regexp` — regular expression match

Use `regexp` for text pattern matching.

Client syntax:

```json
{
  "regexp": {
    "documentTitle": ".*Post AG.*"
  }
}
```

MongoDB output:

```json
{
  "documentTitle": {
    "$regex": ".*Post\\ AG.*",
    "$options": "i"
  }
}
```

Meaning:

> Find titles containing `Post AG`, ignoring uppercase/lowercase differences.

Rules:

| Client pattern | Meaning |
|---|---|
| `".*value.*"` | substring match |
| `"value"` | exact match |

Special regex characters are escaped automatically.

Important:

> Pattern `".*value.*"` must be at least 5 characters total. Shorter patterns return HTTP 400.

Implementation:

```text
JsonStoreSearchQueryUtil.handleQueryRegexp()
getRegexSearch()
escapeSpecialRegexChars()
```

---

## `range` — compare numbers or dates

Use `range` for values between boundaries.

Client syntax:

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

Comparison keys:

| Key | Meaning |
|---|---|
| `gte` | greater than or equal |
| `lte` | less than or equal |
| `gt` | greater than |
| `lt` | less than |

Date fields are converted with MongoDB `$dateFromString`.

Numeric example:

```json
{
  "range": {
    "_files.reference._sizeInBytes": {
      "gte": 1,
      "lte": 52428800
    }
  }
}
```

Meaning:

> Find documents whose referenced file size is between 1 byte and 50 MB.

Implementation:

```text
JsonStoreSearchQueryUtil.handleQueryRange()
buildComparisonOperatorQuery()
```

---

## `and` — all conditions must match

Client syntax:

```json
{
  "and": [
    { "exists": { "field": "user.id" } },
    { "exists": { "field": "user.email" } }
  ]
}
```

Meaning:

> Match only documents where both `user.id` and `user.email` exist.

MongoDB output:

```json
{
  "$and": [
    { "...": "..." },
    { "...": "..." }
  ]
}
```

---

## `or` — at least one condition must match

Client syntax:

```json
{
  "or": [
    { "regexp": { "documentTitle": ".*Post.*" } },
    { "regexp": { "senderName": ".*Post.*" } }
  ]
}
```

Meaning:

> Match documents where title contains `Post` OR sender name contains `Post`.

MongoDB output:

```json
{
  "$or": [
    { "...": "..." },
    { "...": "..." }
  ]
}
```

