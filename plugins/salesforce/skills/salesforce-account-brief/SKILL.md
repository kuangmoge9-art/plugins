---
name: salesforce-account-brief
description: Build Salesforce-grounded account intelligence, meeting prep, opportunity deep dives, contact maps, case context, and recent activity briefs. Use when the user asks for an account or opportunity summary, customer meeting prep, buying committee context, account risks, next-best actions, or a consolidated brief from Salesforce account, opportunity, contact, case, task, and event records.
allowed-tools:
  - describe_global
  - describe_sobject
  - get_current_user
  - get_sobject_record
  - query_soql
  - query_more
  - search_sosl
---

# Salesforce Account Brief

Use this workflow for read-only Salesforce briefs that combine account, opportunity, contact, case, task, and event context. Read `../../references/provider-api-rules.md` when relationship names, record-type-sensitive fields, pagination, or UI/display context matter.

## Workflow

1. Resolve the target account or opportunity first. Prefer exact Salesforce IDs or URLs; otherwise use `search_sosl` or narrow SOQL queries to find candidates.
2. Confirm the needed object and field API names with `describe_sobject` when the fields are non-obvious or custom.
3. Read only the fields needed for the brief. Include `Id`, names, owners, stage/status, dates, amounts, priority, and last activity fields when relevant.
4. Pull adjacent context selectively:
   - account profile and owner fields;
   - open opportunities and current stage/amount/close date;
   - contacts or buying committee records;
   - open cases and SLA or priority signals;
   - recent tasks, events, or activity history.
5. Use `query_more` only when the user needs more than the first page.
6. For relationship-heavy briefs, confirm relationship names with metadata before using nested SOQL. Do not guess child relationship names.
7. If fields are missing from a UI/layout-oriented read, retry with explicit fields before concluding Salesforce does not expose them.

## Output

Return a compact business-facing brief with:

- resolved Salesforce record links;
- current state and important risks;
- recent activity or evidence used;
- gaps or fields that should be verified live;
- suggested next actions, clearly separated from facts.

Do not write back to Salesforce from this skill. Route explicit changes through `salesforce-writeback`.
