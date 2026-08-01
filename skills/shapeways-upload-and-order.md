---
name: Upload a model and place a Shapeways order
description: Authenticate, upload a 3D model, choose a material and shipping option, and place a manufacturing order through the Shapeways API.
api: openapi/shapeways-openapi.yml
operations: [uploadModel, listMaterials, getShippingOptions, placeOrder, getOrder]
---

# Upload a model and place a Shapeways order

Use the Shapeways API (`https://api.shapeways.com`) to turn a 3D model file into a manufacturing order.

## Authenticate
1. Obtain a Client ID and Client Secret from the Manage Apps dashboard.
2. For your own account, request a bearer token with the `client_credentials` grant at `https://api.shapeways.com/oauth2/token`. For acting on another user's account, use the `authorization_code` grant. Tokens expire in 3600s; renew with `refresh_token`.
3. Send `Authorization: Bearer <access_token>` on every request.

## Steps
1. `listMaterials` (`GET /materials/v1`) — pick a `materialId` the model will be printed in.
2. `uploadModel` (`POST /models/v1`) — supply `file`, `fileName`, `hasRightsToModel: true`, `acceptTermsAndConditions: true`, and optionally `defaultMaterialId`. Capture the returned `modelId`. Check `printable` before ordering.
3. `getShippingOptions` (`GET /cart/shipping-options/v1`) — pass the destination `country` (ISO 3166) to get a `shippingOptionId`.
4. `placeOrder` (`POST /orders/v1`) — supply the shipping address fields, `items` (each `modelId` + `materialId` + `quantity`), `paymentMethod`, and the chosen `shippingOption`. Capture `orderId`.
5. `getOrder` (`GET /orders/{orderId}/v1`) — poll for `ordersStatus`.

## Rules
- Every response carries a `result` field (`success`/`failure`); treat `failure` as an error even on HTTP 200.
- Write operations (`uploadModel`, `placeOrder`) are NOT documented as idempotent — do not blindly retry; re-check state with `getOrder`/`getModel` first.
- Back off on HTTP 429 (rate limited). Handle 401 by refreshing the token.
