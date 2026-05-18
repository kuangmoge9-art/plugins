---
name: salesforce-querying
description: Choose and author Salesforce SOQL and SOSL requests. Use when a Salesforce task requires searching records, filtering text, explaining query failures, selecting fields, paginating query results, or deciding whether query_soql or search_sosl is the right tool. Especially use this for long text fields such as Task.Description, where Salesforce can allow reads but reject SOQL WHERE filters.
allowed-tools:
  - describe_global
  - describe_sobject
  - query_soql
  - query_more
  - search_sosl
---

# Salesforce Querying

Use this skill for Salesforce query construction and query failure recovery. Read `../../references/provider-api-rules.md` when the task involves metadata, relationship fields, pagination, high-volume result sets, UI-aware reads, or write follow-ups.

## Choose The Primitive

- Use `query_soql` for structured object reads, relationship fields, ordering, aggregation, and filters on fields Salesforce marks as filterable.
- Use `search_sosl` for keyword search, object-unclear search, cross-object search, and text search across fields that can be returned but not filtered in SOQL.
- Do not rewrite a user's SOQL into SOSL silently. If a SOQL text filter is unsupported, explain the tool switch briefly and run the equivalent SOSL search when the task is still clear.

## SOQL Rules

- Always include `Id` when returning records that may be shown, linked, or used for a follow-up action.
- Keep selected fields minimal and add a `LIMIT` for exploratory queries.
- Treat `batch_size` as a page-size hint, not a hard total limit. Use SOQL `LIMIT` when the total result count must be capped.
- When `done` is false, use `query_more` only when the requested result needs the next page or complete scoped coverage.
- Do not use `WHERE` filters on fields that Salesforce reports as not filterable.
- Treat readable but not filterable text fields as SOSL search candidates.
- Long text and rich text fields are common traps: they may be readable in `SELECT` but rejected in `WHERE`.
- For relationship queries, use describe metadata to confirm relationship field paths and child relationship names before querying.
- For field discovery by label or API name, a narrow `FieldDefinition` SOQL query is acceptable after the target object is known.

Example field-discovery query:

```sql
SELECT DurableId, QualifiedApiName, Label, DataType
FROM FieldDefinition
WHERE EntityDefinition.QualifiedApiName = 'Task'
AND (Label LIKE '%description%' OR QualifiedApiName LIKE '%Description%')
ORDER BY Label
LIMIT 50
```

## SOSL Rules

- Use `FIND {...} IN ALL FIELDS RETURNING Object(FieldA, FieldB, ...)` when the user wants text search.
- Escape or simplify user-provided special characters before placing text inside `FIND {...}`.
- Keep `RETURNING` fields minimal and include `Id`.
- Remember that `search_sosl` returns grouped search results, not the SOQL `totalSize` and `records` envelope.

Example `Task.Description` search:

```sql
FIND {Codex GTM Seed} IN ALL FIELDS RETURNING Task(Id, Subject, Description LIMIT 10)
```

Use that pattern instead of:

```sql
SELECT Id, Subject, Description
FROM Task
WHERE Description LIKE '%Codex GTM Seed%'
LIMIT 10
```

Salesforce can reject the SOQL form even though `Description` can be selected.

## Recovery From Query Errors

- If Salesforce says a field cannot be filtered, move that condition to SOSL when it is text search, or choose a different filterable field.
- If a field or object is unknown, call `describe_sobject` or a narrow `FieldDefinition` query before retrying.
- If the first result set is too broad, tighten by object, date, owner, status, or another confirmed filterable field rather than adding unsupported text filters.
- If the user asks for a large export or bulk write workflow, say that the current connector does not expose Bulk API or Composite API and ask for a narrower synchronous scope or an approved custom endpoint.
