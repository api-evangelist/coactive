---
name: Build and refine a visual concept
description: Create a Coactive concept, label candidate assets, and read its prediction estimation to tag and retrieve matching visual content.
api: openapi/coactive-ai-openapi-original.json
operations:
  - create
  - get-label-candidates
  - update-labels
  - get-concept-prediction
  - list
---

# Build and refine a visual concept

Concepts let you teach Coactive a custom visual idea and apply it across a dataset.

## Prerequisites
- Bearer access token from `POST /api/v0/login` (see `authentication/coactive-ai-authentication.yml`).
- An ingested dataset with searchable assets (`dataset_id`).

## Steps
1. **Create the concept** — `create` (`POST /api/v1/concepts`) with a unique name scoped to the dataset. Concept names must be unique within a dataset.
2. **Get label candidates** — `get-label-candidates` (`GET /api/v1/concepts/{concept_id}/candidates`) to fetch representative assets to label positive/negative.
3. **Apply labels** — `update-labels` (`PATCH /api/v1/concepts/{concept_id}/labels`) with your positive/negative decisions to train the concept.
4. **Check quality** — `get-concept-prediction` (`GET /api/v1/concepts/{concept_id}/prediction_estimation`) to read the estimated prediction quality; iterate steps 2–3 until satisfactory.
5. **List concepts** — `list` (`GET /api/v1/concepts`, paginated via `offset`/`limit`) to manage the concept catalog.

## Conventions
- Offset pagination + `{ meta, data }` envelope; RBAC governs who may edit concepts. See `conventions/coactive-ai-conventions.yml`.
- Handle 422 validation errors from `detail[]`; a 404 means the `concept_id` or dataset no longer exists.
