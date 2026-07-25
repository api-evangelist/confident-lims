---
name: Onboard a testing client and create an order
description: As a testing lab, create or find a testing client, place a multi-sample order,
  and read it back on the Confident Cannabis / Confident LIMS API.
api: openapi/confident-lims-labs-openapi.json
operations: [getClients, createClient, inviteUserToClient, createOrder, getOrderDetails, editOrder, verifyOrder]
---

# Create a client and an order

Base URL: `https://api.confidentcannabis.com` — paths under `/v0/labs/`.

## Authentication
Send `X-ConfidentCannabis-APIKey`, `X-ConfidentCannabis-Signature` (HMAC-SHA256), and
`X-ConfidentCannabis-Timestamp` (unix seconds, within 30s of server time) on every request.

## Steps
1. **Find or create the client.** `getClients` (`GET /v0/labs/clients`) to search; if absent,
   `createClient` (`POST /v0/labs/clients`). Optionally `inviteUserToClient`
   (`POST /v0/labs/client/{client_id}/invite`) to give the client portal access.
2. **Create the order.** `createOrder` (`POST /v0/labs/order`) with `client_id`,
   `lab_internal_id`, and the `samples` array.
3. **Read it back.** `getOrderDetails` (`GET /v0/labs/order/{order_id}`).
4. **Amend if needed.** `editOrder` (`PATCH /v0/labs/order/{order_id}`).
5. **Verify.** `verifyOrder` (`POST /v0/labs/order/{order_id}/status/verify`) to move it into
   the testing workflow.

## Conventions & errors
- Lifecycle transitions are explicit endpoints (verify / unverify / complete / revise /
  cancel / uncancel) — see lifecycle/confident-lims-lifecycle.yml. Prefer them over retries.
- Pagination: `start` + `limit`, stop when `more_results` is false.
- Errors use the custom `{success,error_code,error_message}` envelope
  (errors/confident-lims-problem-types.yml).
