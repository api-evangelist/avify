---
name: Create an Avify order from a cart
description: Build a cart, total it, and create an order in Avify, then track it via webhooks.
api: graphql/avify-graphql-operations.yml
operations: [addCartProduct, updateCartProduct, cartTotals, createOrder, paymentMethods]
---

# Create an Avify order

Use the Avify GraphQL API to add products to a cart, total it, and create an order.

## Auth
- Send the `api-key` header on every request (no Bearer). Generate a token in the
  dashboard: Configuración > Integraciones > API. Endpoint: `https://api.avify.com/graphql`
  (sandbox: `https://sandboxapi.avify.co/graphql`).

## Steps
1. Verify connectivity with `apiTest`.
2. Add items to a cart with `addCartProduct(products, sessionCookie, cartId)`; adjust
   with `updateCartProduct` if needed. Keep the returned `cartId` + `sessionCookie`.
3. Read the order total with `cartTotals(sessionCookie, cartId)` (subtotal, tax,
   shipping, discount, total, currency, items).
4. List accepted payment codes with `paymentMethods`.
5. Create the order with `createOrder(...)`. Optionally attach a `metadata` object
   (max 100kb; string/boolean/number values only).

## Conventions & errors
- Order status flows: `pending` -> `processing` -> `complete` (or `canceled` / `closed`).
- Errors return a custom envelope `{"error":{"code","displayMessage","lang"},"status"}` —
  e.g. `A-34` / `AT-145` mean the api-key is missing or invalid.
- No idempotency key is available; do not blindly retry `createOrder`.
- Track order lifecycle with the `create_order` and `update_order` webhooks.
