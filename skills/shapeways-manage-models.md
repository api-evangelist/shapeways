---
name: Manage Shapeways 3D models
description: List, inspect, and delete the 3D models in a Shapeways account, checking printability and available materials.
api: openapi/shapeways-openapi.yml
operations: [listModels, getModel, deleteModel, getMaterial]
---

# Manage Shapeways 3D models

Maintain the catalog of 3D models on a Shapeways account.

## Authenticate
Send `Authorization: Bearer <access_token>` (OAuth 2.0; see the upload-and-order skill for token flows).

## Steps
1. `listModels` (`GET /models/v1`) — page through the account's models with the `page` query param (36 per page). Read `models[]`.
2. `getModel` (`GET /models/{modelId}/v1`) — inspect a single model's `printable` status, `materials`, `modelVersion`, and `restrictions`.
3. `getMaterial` (`GET /materials/{materialId}/v1`) — resolve material `title`, `restrictions`, and `rejections` before offering a model in that material.
4. `deleteModel` (`DELETE /models/{modelId}/v1`) — remove a model; confirm `result: success`.

## Rules
- Check `printable` and material `rejections`/`restrictions` before assuming a model can be ordered in a given material.
- Every response carries a `result` field; `nextActionSuggestions` hints at logical follow-up calls.
- Deletion is irreversible — confirm the `modelId` first.
