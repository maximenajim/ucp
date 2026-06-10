<!--
   Copyright 2026 UCP Authors

   Licensed under the Apache License, Version 2.0 (the "License");
   you may not use this file except in compliance with the License.
   You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

   Unless required by applicable law or agreed to in writing, software
   distributed under the License is distributed on an "AS IS" BASIS,
   WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
   See the License for the specific language governing permissions and
   limitations under the License.
-->

# Catalog Search Capability

* **Capability Name:** `dev.ucp.shopping.catalog.search`

Performs a search against the business's product catalog. Supports free-text
queries, filtering by category and price, and pagination.

## Operation

| Operation | Description |
| :--- | :--- |
| **Search Catalog** | Search for products using provided inputs and filters. |

### Request

{{ extension_schema_fields('catalog_search.json#/$defs/search_request', 'catalog') }}

### Response

{{ extension_schema_fields('catalog_search.json#/$defs/search_response', 'catalog') }}

## Search Inputs

A valid search request MUST include at least one of: a `query` string,
one or more `filters`, or an extension-defined input. When `query` is
omitted, the request represents a browse operation — the business returns
products matching the provided filters without text-relevance ranking.
Extensions MAY define additional inputs (e.g., visual similarity,
product references).

Implementations MUST validate that incoming requests contain at least one
recognized input and SHOULD reject empty or invalid requests with an
appropriate error. Implementations define and enforce their own rules for
input presence and content — for example, requiring `query`, rejecting
empty `query` strings, or accepting filter-only requests for category browsing.

## Search Filters

Filter criteria for narrowing search results. Standard filters are defined below;
merchants MAY support additional custom filters via `additionalProperties`.

{{ schema_fields('types/search_filters', 'catalog') }}

### Price Filter

{{ schema_fields('types/price_filter', 'catalog') }}

## Location-Aware Search

Catalog search MAY accept `location_filter` to rank or constrain results using
buyer-visible location context. Search remains product-centric: businesses return
products and variants, and variants MAY include method-first
`fulfillment_methods[]` entries that describe whether the product can be
received by shipping, pickup, or other negotiated methods.

Location-bound methods MAY include buyer-visible `destinations[]`. Each entry
is a `fulfillment_destination` discriminated by `destination_type`:
`postal_address`, `retail_location`, `locker`, or `pickup_point`. Catalog
destinations MUST carry `destination_type`.

Destination-level disclosure is opt-in: `discloses_location_availability`
defaults to false in the business fulfillment configuration. Businesses that do
not disclose destination-level inventory omit `destinations[]` and return an
`availability` summary instead. Platforms MUST NOT infer availability from the
presence or absence of destinations in catalog responses.

For location-centric discovery, such as finding nearby stores regardless of a
specific product, use the `dev.ucp.shopping.locations` capability.

## Pagination

Cursor-based pagination for list operations. Cursors are opaque strings
that implementations MAY encode as stateless keyset tokens.

### Page Size

The `limit` parameter is a requested page size, not a guaranteed count.
Implementations SHOULD accept a page size of at least 10. When the
requested limit exceeds the implementation's maximum, implementations
MAY clamp to their maximum silently — returning fewer results without
error. Clients MUST NOT assume the response size equals the requested limit.

### Pagination Request

{{ extension_schema_fields('types/pagination.json#/$defs/request', 'catalog') }}

### Pagination Response

{{ extension_schema_fields('types/pagination.json#/$defs/response', 'catalog') }}

## Transport Bindings

* [REST Binding](rest.md#post-catalogsearch): `POST /catalog/search`
* [MCP Binding](mcp.md#search_catalog): `search_catalog` tool
