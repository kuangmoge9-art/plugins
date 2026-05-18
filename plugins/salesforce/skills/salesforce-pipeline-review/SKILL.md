---
name: salesforce-pipeline-review
description: Review Salesforce pipeline, forecast, stale opportunities, hygiene gaps, close-date risk, stage movement, owner rollups, segment rollups, and quarter summaries. Use when the user asks for open pipeline analysis, commit/upside risk review, deals needing manager review, missing next steps, stale activity, or opportunities that changed stage, amount, close date, or forecast category.
allowed-tools:
  - describe_global
  - describe_sobject
  - get_current_user
  - query_soql
  - query_more
  - search_sosl
---

# Salesforce Pipeline Review

Use this workflow for read-only opportunity and forecast analysis. Read `../../references/provider-api-rules.md` when using custom fields, relationship fields, pagination, or high-volume result sets.

## Workflow

1. Resolve the review scope: quarter or date range, owners, segments, accounts, opportunity type, and forecast categories. For "my" or "me", use `get_current_user`.
2. Inspect `Opportunity` metadata before using custom fields such as segment, forecast, next step, product, or stale-activity signals.
3. Query in narrow passes:
   - aggregate or grouped summaries for totals by stage, owner, segment, close
     month, or forecast category;
   - detail rows for the largest, riskiest, or stale opportunities;
   - activity or task context only for records that need explanation.
4. Flag hygiene gaps such as missing next steps, past close dates, no recent activity, stage/amount/close-date changes, and obvious owner or account mismatches.
5. Use `query_more` only when a complete scoped review needs additional pages.
6. If the requested review would require a very large export or bulk processing, state that Bulk API is not exposed by this connector and narrow to a synchronous slice such as owner, date window, stage, or top-risk records.

## Output

Lead with the review scope and run timestamp. Then return a concise table or grouped list of opportunities with Salesforce links, owner, stage, amount, close date, risk signal, and recommended action.

Keep recommendations evidence-backed. Do not change forecast category, stage, amount, or close date from this skill; route explicit updates through `salesforce-writeback`.
