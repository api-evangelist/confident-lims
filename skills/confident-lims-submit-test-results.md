---
name: Submit lab test results for a sample
description: As a testing lab, retrieve an order's samples, submit analytical test results,
  attach the COA, and advance the order to complete on the Confident Cannabis / Confident LIMS API.
api: openapi/confident-lims-labs-openapi.json
operations: [getOrders, getOrderDetails, getSampleDetails, submitTestResults, uploadSampleCOA, uploadSampleImage, completeOrder]
---

# Submit lab test results

Base URL: `https://api.confidentcannabis.com` — all paths under `/v0/labs/`.

## Authentication
Every request needs three headers (HMAC signing):
- `X-ConfidentCannabis-APIKey`
- `X-ConfidentCannabis-Signature` — HMAC-SHA256 of the request with your API secret
- `X-ConfidentCannabis-Timestamp` — unix seconds, must be within 30s of server time

## Steps
1. **Find work.** `getOrders` (`GET /v0/labs/orders`) with `modified_since_time` / `status_id`
   filters, paging via `start`/`limit` until `more_results` is false.
2. **Open the order.** `getOrderDetails` (`GET /v0/labs/order/{order_id}`) to list its samples.
3. **Inspect a sample.** `getSampleDetails` (`GET /v0/labs/sample/{sample_id}`).
4. **Submit results.** `submitTestResults` (`POST /v0/labs/sample/{sample_id}/test_results`).
5. **Attach the COA / images.** `uploadSampleCOA` (`POST /v0/labs/sample/{sample_id}/coa`) and,
   if needed, `uploadSampleImage` (`POST /v0/labs/sample/{sample_id}/image`).
6. **Complete the order.** `completeOrder` (`POST /v0/labs/order/{order_id}/status/complete`).

## Conventions & errors
- No idempotency key — do not blind-retry `submitTestResults`; re-read with `getSampleDetails` first.
- Errors: custom envelope `{success:false, error_code, error_message}` (see errors/confident-lims-problem-types.yml).
  401 = signing/timestamp problem; 403 = wrong role or not your resource; 404 = bad id.
- Finalized results are pushed to subscribers via webhooks (asyncapi/confident-lims-webhooks.yml).
