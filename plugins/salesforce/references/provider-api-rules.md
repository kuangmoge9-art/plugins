# Salesforce Provider API Rules

Use this reference when a Salesforce task depends on provider semantics rather than only ordinary record lookup.

## Connector Boundary

The Salesforce connector exposes metadata discovery, record reads, SOQL, paginated SOQL continuation, SOSL, sObject create/update/upsert/delete, current user lookup, and approved Apex REST calls.

Do not imply support for Bulk API, Composite API, Data 360, Tableau Analytics, Prompt Builder, Flow invocation, or arbitrary invocable actions unless that capability is exposed through a known approved Apex REST endpoint. For large exports, high-volume write jobs, or analytics surfaces outside the exposed actions, state the missing connector surface and ask for a narrower Salesforce scope or an approved endpoint.

## Metadata Grounding

- Use `describe_global` to resolve object names when the user provides a label or ambiguous object.
- Use `describe_sobject` before non-obvious queries or writes. Check field API names, labels, data type, `filterable`, `createable`, `updateable`, `nillable`, picklist values, references, relationship names, and record type details when those properties affect the task.
- Use `record_type_id` in metadata or write calls when record type changes defaults, picklist choices, required fields, or layout-sensitive behavior.
- For relationship queries, do not guess relationship names. Use metadata: child-to-parent fields use the relationship field path, and parent-to-child subqueries use the child relationship name from describe metadata.

## Query And Search

- Use SOQL for structured filters, relationship fields, ordering, aggregation, owner/date/stage/status filters, and fields confirmed as `filterable`.
- Use SOSL for keyword search, object-unclear search, cross-object search, and text search in fields that can be read but are not SOQL-filterable.
- Select explicit fields. Include `Id` whenever records may be shown, linked, paginated, or used in follow-up actions.
- Treat `batch_size` as a provider page-size hint, not a hard total limit. Use a SOQL `LIMIT` when total rows must be capped.
- When `done` is false, continue with `query_more` only if the user needs the additional page or the requested result requires complete scoped coverage.

## Reads And Display Context

- Prefer explicit field projections for business facts and exports.
- Use UI-aware read modes only when the task needs display-oriented context such as layout fields, optional fields, display values, or record-type-sensitive UI metadata.
- If a requested field is absent from a UI/layout-oriented response, retry with explicit fields before concluding the field is unavailable.

## Writes

- Send only intended fields. Salesforce updates preserve fields omitted from the request, so do not echo unchanged fields into `fields`.
- Before creates, confirm required fields, createable fields, picklists, and record type constraints. Before updates, confirm updateable fields and current values when the change is user-visible or risk-bearing.
- Use `if_unmodified_since` for risky updates when the current `LastModifiedDate` is known and concurrent edits would matter.
- Upsert only when the external ID field and value are verified. If multiple records or no record match the intended external identity, stop and report the ambiguity or create path.
- After a write, verify with returned fields or a read-back query before saying Salesforce was updated.

## Apex REST And Custom Automation

- Prefer standard connector actions when they satisfy the task. Use `call_apex_rest_endpoint` only for approved custom business logic.
- Require the approved method, path, input contract, and expected side effects. Do not invent Apex REST paths, Flow names, Prompt Builder templates, API Catalog endpoints, or invocable action names.
- Treat paths as exact and case-sensitive. Pass only the path under `/services/apexrest`, the required HTTP method, supported query parameters, and JSON body.
- If the endpoint mutates data, verify the created or updated records with returned fields or follow-up Salesforce reads.
