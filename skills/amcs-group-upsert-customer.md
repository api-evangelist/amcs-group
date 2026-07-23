---
name: Create or update an AMCS customer
description: Authenticate and create or update a customer record in the AMCS Platform ERP directory.
api: openapi/amcs-group-erp-openapi-original.json
operations: [Customer_Create, Customer_Get]
---

# Create or update an AMCS customer

## Auth
1. POST your Personal Access Token to `/erp/api/authTokens?PrivateKey={PAT}`; verify `authResult: "ok"` and keep the session cookie.
2. Replay the session cookie against the data base path `/erp/api/integrator/erp`.

## Steps
1. **Upsert** — call `Customer_Create` (`POST /directory/customers`, summary "Create or update"). Supply the customer payload; the operation creates a new customer or updates the matching one.
2. **Verify** — read the returned GUID from the response envelope, then call `Customer_Get` (`GET /directory/customers/{guid}`) to confirm the persisted state.

## Rules
- Send/expect `application/json`; a `415` means an unsupported Content-Type.
- Inspect `ApiResourceStatus.isSuccess` and any `ApiResourceErrors.errors` message in the envelope in addition to the HTTP status.
- No idempotency key exists — do not blindly retry a `POST` on a network timeout without first checking via `Customer_GetChanges` / `Customer_Get` whether it landed.
- See `conventions/amcs-group-conventions.yml` and `errors/amcs-group-problem-types.yml`.
