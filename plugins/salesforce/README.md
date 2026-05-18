# Salesforce

This plugin packages Salesforce connector guidance for Codex. It is scoped to provider-native Salesforce work: metadata discovery, SOQL, SOSL, record reads, and explicit record writes.

## Packaged Skills

- `salesforce`: the top-level workflow for safe Salesforce record work.
- `salesforce-account-brief`: account intelligence, opportunity deep dives, contact/case/activity context, and meeting prep.
- `salesforce-pipeline-review`: pipeline summaries, forecast review, stale deal hygiene, and quarter/owner/segment rollups.
- `salesforce-querying`: focused guidance for choosing SOQL versus SOSL and avoiding unsupported text-field filters.
- `salesforce-writeback`: safe create, update, upsert, and delete workflows.
- `salesforce-automation`: approved Apex REST and custom Salesforce automation calls.

The plugin is app-backed through `.app.json` and uses the canonical Salesforce connector id from the Salesforce app template.

Shared provider-specific behavior lives in `references/provider-api-rules.md`.
