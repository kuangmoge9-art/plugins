# Salesforce Connector

This plugin is intentionally bound to the canonical Salesforce service connector, not a generated app-template instance.

| Lane | App key | Template id | Connector id | Source of truth |
| --- | --- | --- | --- | --- |
| Salesforce CRM | `salesforce` | `templated_apps_Salesforce` | `connector_6095bdb01e9482eb0cac719f6ba313cd` | `lib/applied/connectors/connectors_impl/connectors_impl/salesforce/app_template.py`, via `SALESFORCE_DEFINITION.connector_id` |

Use the Salesforce app for object metadata, SOQL queries, SOSL searches, record reads, and explicit record writes. If the app is unavailable, ask the user to enable Salesforce rather than falling back to unrelated sales or support tools.
