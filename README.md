# HTTP API Design Standard

This repository defines a style guide for HTTP APIs.
The normative text is in [specification/specification.md](specification/specification.md).
The companion command line tool `http-api-spec` checks OpenAPI documents and HTTP responses against the standard.
It lives in its own repository: [floriankraemer/http-api-spec-validator](https://github.com/floriankraemer/http-api-spec-validator).

## Repository layout

- `specification/` holds the standard.

## Comparison with Other API Standards

The following tables compare [this standard](specification/specification.md) with other widely used conventions for JSON-based HTTP APIs.

### Overview

| Standard | Maintainer / status | Scope | Media type | Hypermedia |
|---|---|---|---|---|
| This standard | – | Full API design guideline | `application/json`, `application/problem+json` | Optional |
| **[JSend](https://github.com/omniti-labs/jsend)** | OmniTI Labs; informal convention | Response envelope only | `application/json` | No |
| **[JSON:API](https://jsonapi.org/) 1.1** | Community specification | Request/response format, relationships, query parameters | `application/vnd.api+json` | Links and relationships |
| **[RFC 9457](https://www.rfc-editor.org/rfc/rfc9457.html) Problem Details** | IETF, Proposed Standard | Error responses only | `application/problem+json` | No |
| **[HAL](https://datatracker.ietf.org/doc/html/draft-kelly-json-hal)** | IETF Internet-Draft (expired) | Hypermedia links and embedded resources | `application/hal+json` | Core concept |
| **[OData](https://www.odata.org/) 4.01** | OASIS / ISO standard | Full protocol including query language and metadata | `application/json` (+ OData parameters) | Navigation links |
| **[Google JSON Style Guide](https://google.github.io/styleguide/jsoncstyleguide.xml)** | Google; style guide | Response structure and naming | `application/json` | Optional links (`selfLink`, `nextLink`) |
| **[Microsoft REST API Guidelines](https://github.com/microsoft/api-guidelines)** | Microsoft; guideline | Full API design guideline (Azure) | `application/json` | No |
| **[Zalando RESTful API Guidelines](https://opensource.zalando.com/restful-api-guidelines/)** | Zalando; guideline | Full API design guideline | `application/json`, `application/problem+json` | Optional |

### Feature Comparison

| Standard | Success response | Error response | Pagination | Filtering | Versioning |
|---|---|---|---|---|---|
| This standard | `data` → named entity or collection | RFC 9457 + `code` and context fields | Page-based with top-level `pagination`, or offset/limit | `filter` parameter (JSON:API-based) | Major version in URI (`/v1`), Deprecation/Sunset headers |
| JSend | `{ "status": "success", "data": … }` | `fail` (client) with `data`; `error` (server) with `message`, optional `code` | Not defined | Not defined | Not defined |
| JSON:API | `data` with resource objects (`type`, `id`, `attributes`, `relationships`), plus `included`, `meta`, `links` | `errors` array (`status`, `code`, `title`, `detail`, `source`) | `page[…]` parameters, strategy-agnostic; `first`/`prev`/`next`/`last` links | `filter[…]` parameter, strategy-agnostic; sparse fieldsets, `include`, `sort` | Not defined (media-type profiles/extensions) |
| RFC 9457 | Not defined | `type`, `title`, `status`, `detail`, `instance` + extension members | Not defined | Not defined | Not defined |
| HAL | Resource with `_links` and `_embedded` | Not defined | Via `next`/`prev` links | Not defined | Not defined |
| OData | `value` array with `@odata.context`, `@odata.count` | `error` object (`code`, `message`, `target`, `details`) | `$top`/`$skip`, server-driven `@odata.nextLink` | `$filter` expression language, `$select`, `$expand`, `$orderby` | Protocol version via `OData-Version` header; service versioning up to the service |
| Google JSON Style Guide | `data` with `items` and paging fields; `apiVersion` | `error` object (`code`, `message`, `errors[]`) | `startIndex`, `itemsPerPage`, `totalItems`, `pageIndex`, `totalPages`, `nextLink` inside `data` | Not defined | `apiVersion` field |
| Microsoft REST API Guidelines | Collections in `value` with `nextLink` | `error` object (`code`, `message`, `target`, `details`, `innererror`) | Server-driven via `nextLink`; client-driven via `top`/`skip` | `filter`, `orderby`, `select` (OData-style) | `api-version` query parameter (date-based) |
| Zalando RESTful API Guidelines | Plain resource; collections in an object (e.g. `items`); `snake_case` fields | RFC 9457 (Problem JSON) | Cursor-based preferred; links `next`/`prev` | Query parameters, not standardized | No URI versioning; media-type versioning; avoid breaking changes |

### Summary

-   **JSend** is the simplest envelope.
    Its distinction between `fail` (client problem) and `error` (server problem) matches the failure/error categories of this standard, but it defines nothing beyond the envelope and does not use HTTP semantics or RFC 9457.
-   **JSON:API** is the closest full specification. It shares the `data` top-level field and the `filter` parameter, but prescribes a strict resource-object structure (`type`/`id`/`attributes`) that this standard deliberately avoids in favour of consumer-oriented, named containers.
-   **RFC 9457** is not an alternative but a building block: this standard and the Zalando guidelines use it for all error responses.
-   **HAL** focuses on hypermedia, which this standard treats as optional.
-   **OData** offers the richest query capabilities and machine-readable metadata, at the price of significant complexity; it suits data-centric APIs and generic clients.
-   The **Google JSON Style Guide** is structurally similar (`data` envelope, camelCase), but places paging fields inside `data`, whereas this standard separates control information from data.
-   The **Microsoft** and **Zalando** guidelines are complete design guidelines like this one; they differ mainly in versioning (query parameter or media type instead of URI), envelope (`value` or none) and naming (`snake_case` in Zalando).

---

## License

Copyright © Florian Krämer.

This work is licensed under the [Creative Commons Attribution 4.0 International License (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/deed.en).
