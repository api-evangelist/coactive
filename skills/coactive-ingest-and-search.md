---
name: Ingest a video dataset and search it
description: Create a Coactive dataset, ingest videos into it, wait for ingestion to
  complete, then run natural-language (text-to-image) search over the content.
api: openapi/coactive-openapi-original.json
operations:
- create-dataset-api-v-1-datasets-post
- create-ingest-job-from-videos-api-v-1-ingestion-videos-post
- get-job-status-api-v-1-ingestion-jobs-ingest-job-id-status-get
- text-search-api-v-1-search-text-to-image-post
---

# Ingest a video dataset and search it

Use the Coactive API (`https://api.coactive.ai`) to turn a library of videos into
searchable multimodal intelligence.

## Auth
Exchange credentials for an access token first: `POST /api/v0/login`
- System credentials: HTTP Basic `CLIENT_ID:CLIENT_SECRET`, body `grant_type=client_credentials`.
- Personal token: `Authorization: Bearer <PERSONAL_TOKEN>`, body `grant_type=refresh_token`.

The response returns an `access_token` (`token_type: Bearer`, `expires_in: 3600`). Send
`Authorization: Bearer <access_token>` on every request below and refresh before expiry.

## Steps
1. **Create a dataset** — `create-dataset-api-v-1-datasets-post` (`POST /api/v1/datasets`).
   Keep the returned `dataset_id`.
2. **Ingest videos** — `create-ingest-job-from-videos-api-v-1-ingestion-videos-post`
   (`POST /api/v1/ingestion/videos`) referencing your `dataset_id`. Ingestion is
   asynchronous; keep the returned `ingest_job_id`.
3. **Poll status** — `get-job-status-api-v-1-ingestion-jobs-ingest-job-id-status-get`
   (`GET /api/v1/ingestion/jobs/{ingest_job_id}/status`) until complete. Per-asset failures
   carry an ingestion `ErrorCode` (see errors/coactive-error-codes.yml) — surface
   `permission_denied`, `broken_url`, `unsupported_codec`, etc. to the user.
4. **Search** — `text-search-api-v-1-search-text-to-image-post`
   (`POST /api/v1/search/text-to-image`) with a natural-language query scoped to the
   `dataset_id` to retrieve matching keyframes/assets.

## Rules
- Errors use `{ "detail": "..." }`; `422` returns the FastAPI validation shape with
  `detail[].loc/msg`. See errors/coactive-problem-types.yml.
- No idempotency key — do not blindly retry POSTs; check job/query state first.
- Pagination on list endpoints is `limit`/`offset` (some use `page`/`per_page`).
