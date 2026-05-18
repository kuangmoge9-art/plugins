---
name: salesforce-automation
description: Invoke approved Salesforce automation or custom API surfaces through the connector. Use when the user asks to run an approved Apex REST endpoint, Salesforce Flow-backed action, invocable action, API Catalog custom endpoint, or approved custom Salesforce automation and the endpoint path, method, inputs, and side effects are known.
allowed-tools:
  - describe_global
  - describe_sobject
  - get_sobject_record
  - query_soql
  - search_sosl
  - call_apex_rest_endpoint
---

# Salesforce Automation

Use this workflow for approved Salesforce custom automation exposed through `call_apex_rest_endpoint`. Read `../../references/provider-api-rules.md` before invoking custom endpoints.

## Preconditions

- The user or trusted context must provide the approved endpoint path, method, required inputs, and expected side effects.
- Do not invent Apex REST paths, Flow names, invocable action names, or API Catalog endpoints.
- Treat Apex REST paths as exact and case-sensitive. Pass only the path under `/services/apexrest`.
- If the endpoint mutates Salesforce data, treat it as consequential and restate the target and expected change before calling it when there is any ambiguity.
- Use ordinary Salesforce reads first when the task can be completed without a custom endpoint.

## Workflow

1. Resolve any Salesforce record IDs needed as inputs.
2. Validate fields and object names with metadata when the endpoint contract depends on them.
3. Call `call_apex_rest_endpoint` with the exact method, path, query params, and JSON body required by the approved contract.
4. Verify the result with returned fields or follow-up Salesforce reads when the endpoint creates or updates records.

## Output

Summarize the endpoint called, target records, response status or payload, and verification performed. Say plainly when an endpoint is unavailable or when the request needs an approved path before it can proceed.
