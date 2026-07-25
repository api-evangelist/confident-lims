---
name: Retrieve your orders, samples, and results as a testing client
description: As a testing client, read your account, list orders and samples, and pull
  finalized results and COAs from the Confident Cannabis / Confident LIMS API (read-only).
api: openapi/confident-lims-clients-openapi.json
operations: [getClient, getOrders, getOrderDetails, getSamples, getSampleDetails, getLabs, getLabDetails]
---

# Retrieve client-side results

Base URL: `https://api.confidentcannabis.com` — paths under `/v0/clients/`. Client credentials
are **read-only** (GET).

## Authentication
Send `X-ConfidentCannabis-APIKey`, `X-ConfidentCannabis-Signature` (HMAC-SHA256), and
`X-ConfidentCannabis-Timestamp` (unix seconds, within 30s of server time).

## Steps
1. **Confirm identity.** `getClient` (`GET /v0/clients/client`).
2. **List orders.** `getOrders` (`GET /v0/clients/orders`) — page with `start`/`limit`, use
   `modified_since_time` to pull only what changed since your last sync (webhook-free polling).
3. **Open an order.** `getOrderDetails` (`GET /v0/clients/order/{order_id}`).
4. **List / open samples.** `getSamples` (`GET /v0/clients/samples`) then `getSampleDetails`
   (`GET /v0/clients/sample/{sample_id}`) for full results and COA references.
5. **Resolve labs.** `getLabs` (`GET /v0/clients/labs`) and `getLabDetails`
   (`GET /v0/clients/lab/{lab_id}`).

## Conventions & errors
- Response envelope: `{success:true, <collection>:[...], more_results:bool}`.
- Prefer webhooks (asyncapi/confident-lims-webhooks.yml) over polling when you can receive them.
- Errors use the custom `{success,error_code,error_message}` envelope; 403 means the resource
  is not owned by your credential.
