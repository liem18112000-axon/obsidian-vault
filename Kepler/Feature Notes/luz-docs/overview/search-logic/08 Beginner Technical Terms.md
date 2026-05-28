---
type: technical-glossary
topic: luz-docs search logic
level: beginner
---

# 08 Beginner Technical Terms

Parent: [[Kepler/Feature Notes/luz-docs/overview/search-logic.md]]

Related:
- [[01 Big Picture]]
- [[03 Query Operators]]
- [[05 Server Filters and Aggregation Pipeline]]
- [[06 Facets]]

---

## API

An **API** is a way for one program to talk to another program.

Here:

```text
frontend/client -> luz-docs API -> database
```

The client does not directly access MongoDB. It sends HTTP requests to `luz-docs`.

---

## REST

**REST** is a common style for web APIs.

It uses URLs and HTTP methods like:

- `GET`
- `POST`
- `PUT`
- `DELETE`

This API uses `POST` because the search request body can be complex JSON.

---

## Endpoint

An **endpoint** is one URL path that does a specific job.

Example:

```text
POST /{tenant_id}/documents/search
```

This endpoint searches documents.

---

## Tenant

A **tenant** is one customer/account/organization in a multi-tenant system.

`{tenant_id}` tells the backend which tenant's documents to search.

---

## JSON

**JSON** is a text format for structured data.

Example:

```json
{
  "name": "Alice",
  "age": 30
}
```

This API accepts JSON request bodies and returns JSON responses.

---

## Query DSL

**DSL** means Domain-Specific Language.

A **query DSL** is a small language designed specifically for describing searches.

Example:

```json
{
  "term": {
    "senderName": "mobiliar"
  }
}
```

This is not raw MongoDB. It is the API's own search language.

---

## Elasticsearch-inspired

Elasticsearch is a search engine with a JSON query style.

This API is "Elasticsearch-inspired" because it uses similar ideas like:

- `term`
- `terms`
- `range`
- `and`
- `or`
- facets/aggregations

But it translates them to MongoDB, not Elasticsearch.

---

## MongoDB

MongoDB is a document database.

It stores data as documents, usually BSON/JSON-like objects.

Example:

```json
{
  "_id": "abc",
  "documentTitle": "Invoice"
}
```

---

## Aggregation pipeline

A MongoDB **aggregation pipeline** is a list of processing stages.

Example:

```text
$match -> $sort -> $skip -> $limit -> $project
```

Each stage receives documents, does something, and passes results to the next stage.

---

## `$match`

`$match` filters documents.

Beginner meaning:

> Keep only documents that satisfy this condition.

---

## `$sort`

`$sort` orders documents.

Example:

```text
newest first
```

---

## `$lookup`

`$lookup` joins data from another collection.

Beginner meaning:

> Use an id from this document to fetch related data from another collection.

In this logic, it can load folder data for documents.

---

## `$addFields`

`$addFields` adds new fields or changes existing fields during the pipeline.

Example:

```text
add _folderNames to the result
```

---

## `$skip`

`$skip` skips a number of results.

Used for pagination.

---

## `$limit`

`$limit` restricts how many results are returned.

Used for pagination.

---

## `$project`

`$project` chooses which fields appear in the output.

This is how `includes` and `excludes` are applied.

---

## `$unwind`

`$unwind` splits an array into multiple pipeline items.

Example:

```json
{ "folderIds": ["A", "B"] }
```

After unwind:

```text
one item with folderId A
one item with folderId B
```

---

## `$group`

`$group` groups documents together.

In the count pipeline, grouping by `_id` helps avoid counting the same document more than once.

---

## Soft delete

**Soft delete** means a record is marked as deleted but not physically removed from the database.

Example:

```json
{
  "_deletionStatus": "true"
}
```

Normal search hides soft-deleted records unless `include-deleted-records=true`.

---

## Security class

A **security class** is a label/code used to decide who can see a document or folder.

Beginner meaning:

> A document is visible only if the user has the right permission code.

---

## Credential mapping

**Credential mapping** connects a caller's identity/token to what personal documents they are allowed to see.

It is an extra security filter.

---

## Facet

A **facet** is a grouped count.

Example:

```text
Sender
- Mobiliar (10)
- UBS (2)
```

Facets help build search filter sidebars.

---

## Aggregation

An **aggregation** summarizes data.

Examples:

- count by sender;
- max date per group;
- min file size per group.

---

## Regex / regular expression

A **regular expression** is a text pattern.

Example:

```text
.*Post.*
```

Meaning:

> any text before, then `Post`, then any text after.

In this API, regex is used for fulltext-like matching.

---

## Case-insensitive

**Case-insensitive** means uppercase and lowercase are treated as equal.

Example:

```text
post
Post
POST
```

all match.

---

## ObjectId

An **ObjectId** is a MongoDB id type.

It is often written as a 24-character hex string:

```text
66581234567890abcdef1234
```

The API has special handling for `_id` values because strings must be converted to ObjectIds before comparison.

---

## HTTP 400

HTTP 400 means **Bad Request**.

Beginner meaning:

> The client sent something invalid.

Example:

- passing `facets` to `/count`;
- sending an invalid regex pattern;
- sending an invalid `_id`.

---

## Top-k optimization

**Top-k** means "give me the best first K results."

Example:

```text
Sort by newest and return first 10.
```

If the database can sort and limit efficiently, it avoids processing unnecessary results.

