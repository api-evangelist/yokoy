---
name: Export approved expenses to a finance system
description: Retrieve Yokoy expenses and trigger a finance export, then collect the generated artefacts.
api: openapi/yokoy-openapi-original.json
operations: [listExpenses, getExpense, triggerExpenseExport, listExportJobs, getExportStatus, getExportArefacts]
---

# Export approved expenses to a finance system

Use this skill to pull expense data out of Yokoy and hand it to an ERP / accounting system.

## Auth
Obtain a bearer token via the OAuth2 client-credentials flow (POST `https://accounts.yokoy.ai/oauth2/token`, Basic auth = client_id:client_secret, `grant_type=client_credentials`). All requests are scoped to `/v1/organizations/{organizationId}`. See `authentication/yokoy-authentication.yml`.

## Steps
1. **`listExpenses`** — page through expenses using the `cursor` + `count` query params; narrow with the `filter` query param (see `conventions/yokoy-conventions.yml`).
2. **`getExpense`** — fetch full detail (line items, tax items, journal entry) for each expense you intend to export.
3. **`triggerExpenseExport`** — POST to the legal entity's `expense-export-facilities/{exportFacility}/export-tasks` to start an export job.
4. **`getExportStatus`** — poll the returned export task until it completes (or list them with `listExportJobs`).
5. **`getExportArefacts`** — download the generated journal-entry artefacts once the task is done.

## Rules
- Send the optional `X-Yk-Correlation-Id` header so the export can be traced.
- Handle the `{ code, message }` error envelope; back off on `429 TooManyRequests`. See `errors/yokoy-error-codes.yml`.
