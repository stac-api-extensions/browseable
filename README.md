# STAC API - Browseable Extension Specification

- **Title:** Browseable
- **OpenAPI specification:** TBD
- **Conformance Classes:**
  - <https://api.stacspec.org/v1.0.0-rc.3/browseable>
- **Scope:** STAC API - Core
- **[Extension Maturity Classification](https://github.com/radiantearth/stac-api-spec/tree/main/README.md#maturity-classification):** Proposal
- **Dependencies:**
  - [STAC API - Core](https://github.com/radiantearth/stac-api-spec/tree/main/core)
- **Owner**: @philvarner

## Browseable

The Browseable conformance class (<https://api.stacspec.org/v1.0.0-rc.2/browseable>) provides a formal way
to advertise that all Items in the Catalog presented by a STAC API may be reached by following `child` and
`item` link relations. In a non-API STAC Catalog, all items must be reachable via these relations to be
considered in the catalog. With a STAC API Catalog, items are considered to be in the catalog if they are
accessible via `child` and `item` relations **or** search operations using either [STAC API - Features](https://github.com/radiantearth/stac-api-spec/blob/main/ogcapi-features/README.md) or
[STAC API - Item Search](https://github.com/radiantearth/stac-api-spec/blob/main/item-search/README.md) endpoints. When a STAC API Catalog has child
and item link relations, there is ambiguity in whether the same set of items accessible via
search operations
is also accessible via link relations. Browseable clarifies this by unambiguously advertising that
items available via search can also be accessed by following link relations.

Support for this "browse" mode of interaction via link relations is complementary to the dynamic search
capabilities defined in the [STAC API - Features](https://github.com/radiantearth/stac-api-spec/blob/main/ogcapi-features/README.md) and
[STAC API - Item Search](https://github.com/radiantearth/stac-api-spec/blob/main/item-search/README.md) conformance classes.
Conversely, a STAC API implementation may not support browsing, even though the root is a Catalog object,
if it does not have the appropriate `child` and `item` link relations to traverse over the objects in
the catalog.

Search and browse are complementary ways of interacting with the catalog, and allow users to iteratively interrogate the data
to discover what data is available through browse, and search to filter to only relevant data. Supporting both also opens up a catalog to
clients that are oriented towards reading non-API STAC catalogs
(e.g., [STAC Browser](https://github.com/radiantearth/stac-browser)) and those that are oriented towards
searchable STAC API catalogs
(e.g., [PySTAC Client](https://pystac-client.readthedocs.io/), [stac-nb](https://github.com/darrenwiens/stac-nb)).

Recommendations for supporting browse is discussed in [Structuring Catalog Hierarchies](https://github.com/radiantearth/stac-api-spec/blob/main/core/README.md#structuring-catalog-hierarchies).

## Link Relations

This conformance class also requires implementation of the link relations in the
[STAC API - Core](https://github.com/radiantearth/stac-api-spec/blob/main/core) conformance class.

Additionally, `child` relations must exist to child Catalogs and Collections and `item` relations to Items, such that
every Item in the Catalog can be accessed by traversing these relations.

| **rel** | **href** | **From**  | **Description**                        |
| ------- | -------- | --------- | -------------------------------------- |
| `child` | various  | STAC Core | The child STAC Catalogs & Collections. |
| `item`  | various  | STAC Core | The child STAC Items.                  |

Note that there is a different link relation `items` (plural)
used by the *STAC API - Features* conformance class that links from a collection resource
(at the `/collections/{collectionId}` endpoint) to the items in
that collection (at the `/collections/{collectionId}/items` endpoint). Both of these endpoints are
[derived from OGC API - Features](https://docs.opengeospatial.org/is/17-069r3/17-069r3.html#_items_).

## Endpoints

This conformance class defines no endpoints.

## Example Landing Page for STAC API - Browseable

This JSON is what would be expected from an API that implements *STAC API - Browseable*. Note that the
`conformsTo` array contains `https://api.stacspec.org/v1.0.0-rc.1/browseable` and the `links` array
contains `child` link relations.  The semantics of this conformance class imply that the the catalogs
linked to by these `child` link relations then have further `child` or `item` link relations that
eventually reach all items in this catalog.

```json
{
    "stac_version": "1.0.0",
    "id": "example-stac",
    "title": "A simple STAC API Example, implementing STAC API - Browseable",
    "description": "This Catalog aims to demonstrate the a simple landing page",
    "type": "Catalog",
    "conformsTo" : [
        "https://api.stacspec.org/v1.0.0-rc.1/core",
        "https://api.stacspec.org/v1.0.0-rc.1/browseable"
    ],
    "links": [
        {
            "rel": "self",
            "type": "application/json",
            "href": "https://stac-api.example.com"
        },
        {
            "rel": "root",
            "type": "application/json",
            "href": "https://stac-api.example.com"
        },
        {
            "rel": "service-desc",
            "type": "application/vnd.oai.openapi+json;version=3.0",
            "href": "https://stac-api.example.com/api"
        },
        {
            "rel": "service-doc",
            "type": "text/html",
            "href": "https://stac-api.example.com/api.html"
        },
        {
            "rel": "child",
            "type": "application/json",
            "href": "https://stac-api.example.com/catalogs/sentinel-2",
        },
        {
            "rel": "child",
            "type": "application/json",
            "href": "https://stac-api.example.com/catalogs/landsat-8",
        }
    ]
}
```
