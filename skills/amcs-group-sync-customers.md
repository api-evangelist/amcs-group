---
name: Sync AMCS customers
description: Authenticate to the AMCS Platform REST API and incrementally sync customer records using the delta-changes endpoint.
api: openapi/amcs-group-erp-openapi-original.json
operations: [Customer_GetCollection, Customer_GetChanges, Customer_Get]
---

# Sync AMCS customers

Keep a local copy of AMCS Platform customers up to date using the ERP Platform delta-sync pattern.

## Auth
1. POST your Personal Access Token to `/erp/api/authTokens?PrivateKey={PAT}` (see `authentication/amcs-group-authentication.yml`).
2. Confirm the JSON body has `authResult: "ok"`, then capture the session token from the `Set-Cookie` header.
3. Send that cookie on every subsequent request against the data base path `/erp/api/integrator/erp`.

## Steps
1. **Initial load** — call `Customer_GetCollection` (`GET /directory/customers`). Page through results with the `page` query parameter until `extra.count` is exhausted.
2. **Incremental sync** — on later runs call `Customer_GetChanges` (`GET /directory/customers/changes`) to fetch only records changed since your last checkpoint.
3. **Hydrate one record** — for any changed GUID, call `Customer_Get` (`GET /directory/customers/{guid}`) to fetch the full entity.

## Rules
- Responses are wrapped in an `ApiResourceResultCollection` / `ApiResourceResultEntity` envelope; read data from the results array and follow `links.associations` / `links.expand` for related resources (`conventions/amcs-group-conventions.yml`).
- Handle `401` by re-authenticating (session expired); handle `400` as a bad filter/parameter (`errors/amcs-group-problem-types.yml`).
- There is no idempotency-key contract — treat writes as non-idempotent.
