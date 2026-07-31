---
ai_hash: d7521b9937c03695
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Request Body Structure
- JSON body
- POST /search
- /count
- from
- size
- query
- facets
- sort
- includes
- excludes
- Pagination
- 04 Query Operators
- 08 Facets
- 07 Aggregation Pipeline
- 02 Endpoints
- search-logic
- response
- search
- filtering
- documentTextContent
- UI
- title
- date
- Glossary
- networks
---

# 03 — Request Body Structure

The JSON body you send with every `POST /search` or `/count` request.

## The skeleton

```json
{
  "from":    0,
  "size":    10,
  "query":   { },
  "facets":  { },
  "sort":    { "fieldName": "asc" },
  "includes": ["field1", "field2"],
  "excludes": ["field3"]
}
```

## Field-by-field (plain English)

| Field | What it means | Default |
|-------|---------------|---------|
| `from` | [[Glossary#Pagination\|Pagination]] offset — skip this many results. `0` = start from the top. | `0` |
| `size` | How many results to return on this page. | `10` |
| `query` | The actual search — a JSON object describing what to match. See [[04 Query Operators]]. | — |
| `facets` | Optional summary counts (grouped by field). See [[08 Facets]]. | — |
| `sort` | Which field to order by, ascending (`"asc"`) or descending (`"desc"`). | — |
| `includes` | Allowlist — return ONLY these fields on each result. | all fields |
| `excludes` | Denylist — return everything EXCEPT these fields. | none |

## Newbie tips

- `from` and `size` together = **pagination**. Page 3 of 10-per-page = `"from": 20, "size": 10`.
- Use `includes` to keep responses small. If your UI only shows title + date, ask for just those two — networks like that.
- `excludes` is good for hiding huge fields like `documentTextContent` (the full OCR'd text of a PDF) when you don't need them.
- `includes` and `excludes` happen at the end of the [[07 Aggregation Pipeline|pipeline]], after filtering. They don't make the search faster, just the response smaller.

---

**Navigation:** [[02 Endpoints|← prev]] · [[search-logic|↑ index]] · [[04 Query Operators|next →]]

%% ai-graph-start %%

**Related notes:**
- [[09 Examples]]
- [[02 Endpoints]]
- [[search-logic]]
- [[Glossary]]
- [[04 Query Operators]]

**Relations:**
- Request Body Structure — *describes* — JSON body
- JSON body — *used_with* — POST /search
- JSON body — *used_with* — /count
- JSON body — *contains_field* — from
- JSON body — *contains_field* — size
- JSON body — *contains_field* — query
- JSON body — *contains_field* — facets
- JSON body — *contains_field* — sort
- JSON body — *contains_field* — includes
- JSON body — *contains_field* — excludes
- from — *is_a* — Pagination offset
- from — *has_default_value* — 0
- size — *defines* — number of results per page
- size — *has_default_value* — 10
- query — *describes* — what to match
- query — *references* — 04 Query Operators
- facets — *provides* — summary counts
- facets — *references* — 08 Facets
- sort — *defines* — field order
- includes — *is_a* — Allowlist
- includes — *returns_only* — specified fields
- includes — *has_default_value* — all fields
- excludes — *is_a* — Denylist
- excludes — *returns_everything_except* — specified fields
- excludes — *has_default_value* — none
- from — *together_with* — size
- from — *enables* — Pagination
- size — *enables* — Pagination
- includes — *keeps* — response small
- UI — *shows* — title
- UI — *shows* — date
- networks — *prefers* — small responses
- excludes — *hides* — documentTextContent
- documentTextContent — *is_a* — huge field
- includes — *executes_in* — 07 Aggregation Pipeline
- excludes — *executes_in* — 07 Aggregation Pipeline
- 07 Aggregation Pipeline — *is_after* — filtering
- includes — *does_not_speed_up* — search
- excludes — *does_not_speed_up* — search
- includes — *makes_smaller* — response
- excludes — *makes_smaller* — response
- Request Body Structure — *preceded_by* — 02 Endpoints
- Request Body Structure — *followed_by* — 04 Query Operators
- Request Body Structure — *is_part_of* — search-logic
- Pagination — *defined_in* — Glossary

%% ai-graph-end %%