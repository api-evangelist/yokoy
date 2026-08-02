---
name: Sync users and cost-center master data
description: Provision users and cost centers in Yokoy from an HR/finance source of truth.
api: openapi/yokoy-openapi-original.json
operations: [listUsers, createUser, modifyUser, listCostObjects, createCostObject, updateCostObject]
---

# Sync users and cost-center master data

Use this skill to keep Yokoy's user directory and cost-center hierarchy aligned with an upstream HRIS/ERP.

## Auth
OAuth2 client-credentials bearer token, scoped to `/v1/organizations/{organizationId}`. Cost centers are nested under `/legal-entities/{legalEntityId}`. See `authentication/yokoy-authentication.yml`.

## Steps
1. **`listCostObjects`** — read existing cost centers for the legal entity (cursor pagination).
2. **`createCostObject`** / **`updateCostObject`** — create or update cost centers; `parentId` builds the hierarchy, `approverId` binds an approver.
3. **`listUsers`** — read existing users.
4. **`createUser`** — provision new users; link `costCenterId`, `policyId`, and `lineManagerId`.
5. **`modifyUser`** — patch changed fields for existing users.

## Rules
- Reconcile by external identifiers before creating to avoid duplicates.
- Cost centers must exist before users reference them (`costCenterId`).
- Handle the `{ code, message }` error envelope; back off on `429`. See `conventions/yokoy-conventions.yml`.
