---
name: Run an asynchronous query and export results
description: Create an asynchronous query over a Coactive dataset, poll it to completion,
  and export the results.
api: openapi/coactive-openapi-original.json
operations:
- create-query-api-v-1-queries-post
- get-query-api-v-1-queries-query-id-get
- export-query-api-v-1-queries-query-id-exports-post
---

# Run an asynchronous query and export results

Coactive queries run asynchronously against an ingested dataset and produce exportable
result sets you can push to a warehouse or BI tool.

## Auth
Obtain a bearer access token via `POST /api/v0/login` (see
authentication/coactive-authentication.yml) and send `Authorization: Bearer <access_token>`.

## Steps
1. **Create the query** — `create-query-api-v-1-queries-post` (`POST /api/v1/queries`).
   Keep the returned `query_id`. The query executes asynchronously.
2. **Poll the query** — `get-query-api-v-1-queries-query-id-get`
   (`GET /api/v1/queries/{query_id}`) until it reports a terminal/complete state.
3. **Export** — `export-query-api-v-1-queries-query-id-exports-post`
   (`POST /api/v1/queries/{query_id}/exports`) to materialize the results.

## Rules
- A `404` means the `query_id` does not exist or is not owned by your organization.
- Errors use `{ "detail": "..." }`; `422` returns the FastAPI validation shape. See
  errors/coactive-problem-types.yml.
- No idempotency key — poll state instead of blindly re-POSTing.
