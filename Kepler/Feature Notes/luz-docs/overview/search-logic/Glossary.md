# Glossary — search-logic terms in plain English

A one-stop reference for every jargon term used in the [[search-logic]] notes. If a term in another note feels mysterious, jump here.

---

## REST API

A way for two programs to talk to each other over the web. One program sends a request (like "give me document 42"), the other sends back an answer. **REST** is just a popular style for how that request and answer are shaped. We use it via URLs and HTTP verbs like `GET`, `POST`.

> In luz-docs you `POST` (send) a JSON body to a URL like `/luz_docs/api/<tenant>/documents/search` and get back JSON.

## JSON

A text format for structured data. Looks like `{ "name": "Alice", "age": 30 }`. Used everywhere because it's human-readable and every programming language understands it.

## DSL (Domain-Specific Language)

A "mini-language" designed for one purpose. SQL is a DSL for databases. luz-docs's query DSL is a JSON-shaped mini-language for asking "find me documents where…". You don't write code in it — you write a JSON object that describes what you want.

## Query

A description of which records you want. "All invoices from 2024" is a query in human language. In luz-docs it's a JSON object using operators like `term`, `range`, `and`.

## Elasticsearch

A popular search engine. Its query language is JSON-shaped and very expressive. luz-docs's query DSL **looks like** Elasticsearch's so anyone who knows Elasticsearch feels at home — but under the hood luz-docs translates to MongoDB, not Elasticsearch.

## MongoDB

A database that stores **documents** (JSON-like records) instead of tables with rows and columns. Each document is a flexible bag of fields. Great for things like luz-docs where every document can have different fields.

## Aggregation Pipeline

MongoDB's way of running a sequence of steps on your data, each step transforming the output of the previous one. Like a factory conveyor belt:

```
raw documents → [filter] → [sort] → [join] → [keep only some fields] → final result
```

Each step is called a **stage** and starts with a `$`, e.g. `$match`, `$sort`, `$lookup`. The order of stages matters — putting `$sort` before `$match` does different work than putting it after.

## `$match`

A pipeline stage that **filters**: keep only documents matching this condition. Equivalent to SQL's `WHERE`.

## `$sort`

A pipeline stage that orders the documents by a field, ascending or descending.

## `$lookup`

A pipeline stage that **joins** with another collection. Like fetching a folder's name by looking it up in the folders collection using the folder ID.

## `$project`

A pipeline stage that picks which fields to return and which to hide. Like SELECT in SQL.

## `$skip` and `$limit`

`$skip: 20` ignores the first 20 results, `$limit: 10` returns at most 10. Together they implement **pagination** (page 3 of 10-per-page = skip 20 limit 10).

## `$expr`

A MongoDB construct that lets you use **aggregation expressions** (like comparing two fields, doing math, parsing dates) inside a `$match`. Needed for the `range` operator because it compares a stored field against a value with type conversion.

## `$or`, `$and`, `$not`

MongoDB's logical operators. `$or` = at least one condition must be true. `$and` = all conditions must be true. `$not` = the condition must be false.

## `$regex`

Tells MongoDB to match a field against a **regular expression** (see below).

## Regex (Regular Expression)

A pattern for matching text. `.*invoice.*` means "anything, then 'invoice', then anything" — i.e. matches any text containing 'invoice'. Special characters like `.` `*` `+` `?` have meanings, which is why luz-docs **escapes** them in user input (turning `.` into `\.` so it matches a literal dot, not "any character").

## Case-insensitive

Doesn't care about UPPER vs lower case. `"Invoice"` matches `"invoice"` and `"INVOICE"`. In MongoDB you get this by passing `$options: "i"` with `$regex`.

## ObjectId

MongoDB's unique-id type — a 24-character hexadecimal string like `5f8d0d55b54764421b7156c3`. Looks like a random hash but contains a timestamp. The `_id` of every document is an ObjectId.

## Field

A named piece of data on a document, like `documentTitle` or `_createdDate`. Same idea as a column in a spreadsheet, but documents can have different fields from each other.

## Array (in MongoDB)

A list of values stored in a single field. `folderIds: ["abc", "def"]` is an array field. You can ask "does this array contain X?" or "is it empty?" with operators like `$size: 0` (length zero) or `folderIds.0` (does index 0 exist?).

## Soft delete

A "delete" that doesn't actually erase the record. Instead a flag like `_deletionStatus = "true"` is set so normal searches hide it, but admins can still recover it. The opposite is a **hard delete** which removes the row permanently.

## Tenant

An isolated customer / organization in a multi-customer system. The same luz-docs server serves many tenants; the `{tenant_id}` in the URL says which tenant's data you mean. One tenant cannot see another's documents.

## Security class

A label on a document or folder that says "only people with this clearance can see this." The server checks the caller's clearance against the document's class and hides the rest. The `skip-security-classes=true` admin flag bypasses this for support work.

## Credential mapping

A per-user permission layer for personal documents. Even if security classes match, a personal document is only returned to the user it was credentialed to.

## Facet

A summary count grouped by some field. Picture an online shop showing "Brand: Nike (12), Adidas (7)" in the sidebar — those counts are facets. In luz-docs, asking for a facet on `senderName` returns a list of senders and how many documents each has.

## Aggregation (in facet sense)

Computing a summary value — count, sum, min, max, distinct list — across many documents. A facet is one form of aggregation.

## Pagination

Returning results in pages. `from` is the offset (skip this many), `size` is the page size. `from: 20, size: 10` = the third page if pages are 10 long.

## Top-k optimization

A speed trick: if you only need the top 10 sorted results, the database can stop sorting once it has 10 — it doesn't have to sort every match. Putting `$sort` early in the pipeline lets MongoDB do this.

## Index (database)

A pre-built lookup structure that makes "find documents where field X = Y" fast, instead of scanning every document. Like the index at the back of a book. The pipeline puts `$sort` on `_id` early because `_id` always has an index.

## HTTP 400 / HTTP status codes

Numeric codes a server returns. `200` = OK. `400` = your request was malformed (bad input). `401` = not authenticated. `404` = not found. `500` = server crashed.

## Pass-through (in code)

When code takes an input and forwards it unchanged. The `term` operator is a pass-through: client sends `{ "fieldName": "value" }`, MongoDB receives the same thing, no transformation.

## Sub-query

A query nested inside another query. In `{ "not": { "term": { ... } } }`, the `term` part is a sub-query of the `not`.

## Recursive dispatch

A coding pattern where a function handles each operator by calling itself on the inner operators. That's how `and` and `or` can contain any other operator, including more `and`s and `or`s — the code "dispatches" each one to itself.

## DSL → MongoDB translation

The job of `JsonStoreSearchQueryUtil.java`: take the user's JSON query (in luz-docs DSL) and rewrite it as a MongoDB query / aggregation pipeline. Like a compiler from one language to another.

---

**Back to:** [[search-logic]]
