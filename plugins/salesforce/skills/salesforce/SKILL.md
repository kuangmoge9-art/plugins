---
name: salesforce
description: Safely work with Salesforce records through the canonical Salesforce connector. Use when the user asks to find, query, search, summarize, create, update, upsert, or delete Salesforce records; inspect Salesforce objects or fields; or resolve record IDs before acting. Prefer metadata discovery over guessing, choose SOQL or SOSL deliberately, and write only when the user explicitly requests a Salesforce change.
allowed-tools:
  - describe_global
  - describe_sobject
  - get_current_user
  - get_sobject_record
  - query_soql
  - query_more
  - search_sosl
  - create_sobject_record
  - update_sobject_record
  - upsert_sobject_record_by_external_id
  - delete_sobject_record
  - call_apex_rest_endpoint
---

# Salesforce

Use this as the top-level workflow for the Salesforce plugin. The Salesforce app is `salesforce` in `.app.json` and is bound to canonical connector id `connector_6095bdb01e9482eb0cac719f6ba313cd`.

If Salesforce tools are not available, stop and ask the user to enable the Salesforce connector. Do not satisfy Salesforce data requests from unrelated sales, support, or warehouse tools unless the user explicitly asks for that fallback.

Read `../../references/provider-api-rules.md` for non-trivial metadata, relationship, pagination, high-volume, write, UI-context, or custom automation tasks.

## Focused Workflows

- Use [salesforce-account-brief](../salesforce-account-brief/SKILL.md) for account intelligence, meeting prep, buying committee, recent activity, cases, and opportunity-context briefs.
- Use [salesforce-pipeline-review](../salesforce-pipeline-review/SKILL.md) for pipeline summaries, forecast review, stale deal hygiene, close-date risk, and owner or segment rollups.
- Use [salesforce-writeback](../salesforce-writeback/SKILL.md) for explicit creates, updates, upserts, task/activity logging, support case creation, opportunity next-step changes, and deletions.
- Use [salesforce-automation](../salesforce-automation/SKILL.md) for approved Apex REST or custom Salesforce automation endpoints.
- Use [salesforce-querying](../salesforce-querying/SKILL.md) when the hard part is choosing SOQL versus SOSL, field filtering, pagination, or query recovery.

## Core Rules

- Prefer discovery over guessing. Use `describe_global` for object matching and `describe_sobject` for field matching before non-obvious queries or writes.
- Include `Id` in SOQL `SELECT` clauses whenever records may be shown or updated.
- Use user-facing labels in final answers when known, and API names only when needed for precision or follow-up.
- Keep result sets narrow. Select only fields needed for the task and add a reasonable `LIMIT` unless the user asked for a complete export.
- Use `query_more` when `query_soql` returns a locator and the user needs more than the first page.
- Use `get_current_user` for requests involving "my", "me", or the current Salesforce owner.
- Write only when the user explicitly asks to create, update, upsert, delete, or call a mutating Apex endpoint.
- After any write, inspect the response and, when `return_fields` can verify the outcome, read back or use returned fields before summarizing the result.
- Do not promise Bulk API, Composite API, Data 360, Tableau Analytics, Prompt Builder, Flow, or arbitrary invocable action support unless those capabilities are exposed through an approved Apex REST endpoint.

## Record Disambiguation

- Prefer exact name or id matches before broad text matching when locating records.
- Treat broad `LIKE '%...%'` filters as candidate discovery, not proof of the canonical record.
- Account names can match regions, subsidiaries, brands, domains, and email-like records. Do not silently pick the first result when multiple plausible records remain.
- Use available business context such as country, website, industry, owner, active opportunities, recent tasks, or the user's wording to disambiguate.
- If one additional targeted filter will not clearly disambiguate the result, present the candidate records to the user before taking write actions or using the record as the basis for a deal summary.

## Query And Search Routing

Use `query_soql` for structured reads and filters on SOQL-filterable fields. Use `search_sosl` for keyword search, object-unclear search, or text search inside fields that Salesforce can read but does not allow in SOQL `WHERE` filters. A key example is `Task.Description`: do not use `WHERE Description LIKE ...`; use SOSL instead.

## Write Safety

- Confirm object API name, record id, and field API names before writes.
- For `update_sobject_record`, send only changed fields in `fields`.
- For `create_sobject_record`, send only intended create fields and include `record_type_id` only when record type affects defaults, picklists, or createability.
- For `upsert_sobject_record_by_external_id`, make sure the external id field and value come from the user or verified metadata.
- Treat `delete_sobject_record` and mutating `call_apex_rest_endpoint` calls as destructive. Restate the target before executing when there is any ambiguity.

## Output

Return concise, business-facing results. Include clickable Salesforce record links when `Id` is available:

- Known object and id: `https://openai.lightning.force.com/lightning/r/<ObjectApiName>/<Id>/view`
- Only id known: `https://openai.lightning.force.com/<Id>`
