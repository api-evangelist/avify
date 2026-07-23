---
name: Manage Avify products and inventory
description: Create products, read inventory by location, and bulk-update stock in Avify.
api: graphql/avify-graphql-operations.yml
operations: [products, product, inventory, storeLocations, createBaseProduct, bulkUpdateStock]
---

# Manage Avify products and inventory

Use the Avify GraphQL API to catalog products and keep stock in sync across locations.

## Auth
- Send the `api-key` header on every request. Endpoint: `https://api.avify.com/graphql`
  (sandbox: `https://sandboxapi.avify.co/graphql`).

## Steps
1. List store locations with `storeLocations` to get the `locationId` values you will
   scope stock to.
2. Browse the catalog with `products(pageNum, pageSize, filter)` — pass `selectMode: "S"`
   for a simplified payload; fetch one item with `product(sku)`.
3. Create a product with `createBaseProduct(createBaseProductInput, stock)`.
4. Read stock with `inventory(sku)` — returns base + child stock via
   `stockByLocation` (qty, reservations, reorderPoint, lowStockAlertEnabled). Omit
   `locationId` upstream for global inventory.
5. Push stock changes in batch with `bulkUpdateStock(stocks, reindexEvent, instantReindex)`.

## Conventions & errors
- Pagination is `pageNum` / `pageSize`.
- Errors return `{"error":{"code","displayMessage","lang"},"status"}`; `A-34` / `AT-145`
  indicate a missing or invalid api-key.
