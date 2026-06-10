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

# Locations Capability

* **Capability Name:** `dev.ucp.shopping.locations`

The locations capability lets platforms discover buyer-visible business
locations such as stores, lockers, and pickup points. It is a location-centric
surface: it answers questions such as "which locations are near this buyer?"
and "when is this location open?"

Per-variant, per-location availability is not returned by this capability.
Product availability remains a catalog concern and is expressed through
method-first `variant.fulfillment_methods[]` results on catalog search and
lookup responses.

The locations capability can report which fulfillment method types a location
supports, such as `pickup`, `curbside`, or `local_delivery`, but that is
location capability metadata only. It does not imply a specific variant is in
stock at that location.

## Request

{{ extension_schema_fields('locations.json#/$defs/locations_request', 'locations') }}

## Response

{{ extension_schema_fields('locations.json#/$defs/locations_response', 'locations') }}

## Entities

### Retail Location

{{ schema_fields('types/retail_location', 'locations') }}

### Location Filter

{{ schema_fields('types/location_filter', 'locations') }}

## Privacy

Buyer-granted precise coordinates belong in `context.geo`. Platforms MUST only
send `context.geo` with buyer consent and for the current commerce task.
Platform-derived coarse location belongs in `signals["dev.ucp.shopping.geo"]`.

Neither field is a checkout store selection. Confirmed store anchoring is a
fulfillment checkout concern.

## Transport Bindings

* REST Binding: `POST /locations/search`
* MCP Binding: `search_locations` tool
