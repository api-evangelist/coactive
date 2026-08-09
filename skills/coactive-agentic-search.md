---
name: Conversational agentic search over a dataset
description: Use Coactive's agentic search to answer natural-language questions over multimodal content, scoped to a dataset, and drill down conversationally.
api: openapi/coactive-ai-openapi-original.json
operations:
  - chat
  - chat-dataset
  - search-videos
---

# Conversational agentic search over a dataset

Agentic Search is Coactive's natural-language interface for finding and refining results through conversation.

## Prerequisites
- Bearer access token from `POST /api/v0/login`.
- A dataset that has finished ingestion.

## Steps
1. **Open a chat** — `chat` (`POST /api/v0/chat`) with a natural-language query to search across content.
2. **Scope to a dataset** — `chat-dataset` (`POST /api/v0/chat/dataset/{dataset_id}`) to constrain the conversation to a single dataset; drill down by referencing returned asset/video IDs in follow-up turns.
3. **Retrieve matching videos** — `search-videos` (`POST /api/v1/search/video`) to pull the underlying video matches for a query.

## Conventions
- Agentic results typically return within ~30s; treat calls as synchronous request/response (no webhooks).
- Auth tokens expire after 3600s — refresh mid-conversation if needed.
- Errors follow the FastAPI envelope (`errors/coactive-ai-problem-types.yml`).
