---
name: Ingest and export supplier invoices
description: Create or upload supplier invoices in Yokoy and export the booked results.
api: openapi/yokoy-openapi-original.json
operations: [createInvoice, uploadAiInvoice, uploadEinvoice, listInvoices, getInvoice, triggerInvoiceExport]
---

# Ingest and export supplier invoices

Use this skill to push supplier invoices into Yokoy for AI processing and approval, then export the booked invoices.

## Auth
OAuth2 client-credentials bearer token, scoped to `/v1/organizations/{organizationId}`; invoice endpoints are nested under `/legal-entities/{legalEntityId}`. See `authentication/yokoy-authentication.yml`.

## Steps
1. Choose the ingest path for the legal entity:
   - **`createInvoice`** — post a fully structured invoice payload.
   - **`uploadAiInvoice`** — upload a document (PDF/image) for AI extraction.
   - **`uploadEinvoice`** — submit a structured e-invoice.
2. **`listInvoices`** — page with `cursor` + `count`; use `filter` to find pending invoices.
3. **`getInvoice`** — read back the processed invoice, its line items and supplier link.
4. **`triggerInvoiceExport`** — POST to `invoice-export-facilities/{exportFacility}/export-tasks` to book the invoices into the finance system; collect artefacts via the export-task operations (see the export-expenses skill).

## Rules
- Suppliers are referenced by `supplierId`; resolve or create them first (`createEntitySupplier` / `createOrgSupplier`).
- Respect the `{ code, message }` error envelope and `429` rate limiting.
