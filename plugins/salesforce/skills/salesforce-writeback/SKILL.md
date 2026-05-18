---
name: salesforce-writeback
description: Safely create, update, upsert, or delete Salesforce records after the user explicitly requests a Salesforce change. Use for updating opportunity next steps, close dates, amounts, stages, creating leads, tasks, activity notes, support cases, upserting by external ID, and deleting confirmed duplicate or test records.
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
---

# Salesforce Writeback

Use this workflow only when the user explicitly asks to create, update, upsert, or delete Salesforce data. Read `../../references/provider-api-rules.md` before non-trivial creates, updates, upserts, deletes, optimistic-concurrency checks, or record-type-sensitive writes.

## Preconditions

- Resolve the exact object API name and record ID before updates or deletes.
- Confirm field API names, updateability, createability, required fields, picklist values, and record type constraints with `describe_sobject` when not already known.
- For ambiguous targets, show candidates and ask for the exact record before writing.
- For destructive deletes, restate the target record and require explicit user confirmation unless the current message already names the exact record and confirms deletion.

## Write Patterns

- `create_sobject_record`: create leads, cases, tasks, events, notes, or other records with only the intended fields.
- `update_sobject_record`: send only changed fields. For opportunity updates, show before-and-after values when practical. Use `if_unmodified_since` when the current `LastModifiedDate` is known and concurrent edits would matter.
- `upsert_sobject_record_by_external_id`: use only when the external ID field and value are verified from the user or metadata. Stop if the external identity is ambiguous.
- `delete_sobject_record`: use only for the confirmed record ID.

For creates and updates, do not send unchanged fields. Preserve user-visible format constraints by writing API-native values, not display-formatted guesses.

## Verification

After any write, inspect the action response. When the response does not include enough detail to prove the outcome, read the record back with `get_sobject_record` or a narrow SOQL query before summarizing.

Return the Salesforce record link, fields changed or created, verification method, and anything Salesforce rejected or left incomplete.
