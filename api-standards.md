# API Standards
## 1. Introduction

These standards shamelessly borrows from the following sources with some content from both being repeated here:

* [Microsoft REST API Guidelines](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md)
* [Google API Design Guide](https://cloud.google.com/apis/design/)

This document describes the Integration and System Development Team's approach to designing [Representational State Transfer (REST)](https://en.wikipedia.org/wiki/Representational_state_transfer) [Application Programming Interfaces (APIs)](https://en.wikipedia.org/wiki/Application_Programming_Interface). REST APIs implemented by the team SHOULD follow these so they are developed consistently.

These standards aim to achieve the following:

* Define consistent practices and patterns for all API endpoints in the Integration and System Development Team.
* Adhere as closely as possible to accepted REST/HTTP best practices in the industry at-large.
* Make accessing Integration and System Development Team Services via REST interfaces easy for all application developers.
* Allow service developers to leverage the prior work of other services to implement, test and document REST endpoints consistently.

### 1.1 Pragmatism

Making a breaking change to a service that predates these guidelines simply for the sake of compliance is not recommended. The service SHOULD try to become compliant at the next version release. When a service adds a new API, that API SHOULD be consistent with the other APIs of the same version.

### 1.2 Keywords

The keywords "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](https://www.ietf.org/rfc/rfc2119.txt).

### 1.3 Updates

This document does not pretend to cover every possible eventuality. If use cases are found which are missing from this standard, the approach must be decided and added. The same is true for any practices detailed here that prove not fit for purpose. They should be updated to a more appropriate approach.

## Conventions

All services return [JavaScript Object Notation (JSON)](https://en.wikipedia.org/wiki/JSON) by default. [Extensible Markup Language (XML)](https://en.wikipedia.org/wiki/XML) is available if specifically requested via an accepts header.

## Versioning 
All APIs MUST be versioned.

Services are versioned using a Major.Minor versioning scheme. The version of the API is embedded in the path of the request URL, at the end of the service root, e.g.:

* `https://api.contoso.com/v1.0/products/users`

## URL Structure

## Supported methods

## Standard request headers

## Standard response headers

## Standard fields

## Response format

Response: Object

| Property | Type    | Required | Value | Description                         |
|----------|---------|----------|-------|-------------------------------------|
| status   | String  | ✔        | "ok"  | message status                      |
| payload  | Payload | ✔        |       | Object containing all returned data |

Payload: Object

| Property    | Type          | Required | Description                |
|-------------|---------------|----------|----------------------------|
| page_number | Number        | ✔        | Number of the current page |
| page_size   | Number        | ✔        | Length of the current page |
| items       | Array<Object> | ✔        | data returned              |
| links       | Links         | ✔        | Navigation links           |

Links: Object

| Property | Type | Required | Description                        |
|----------|------|----------|------------------------------------|
| prev     | Url  |          | Link to previous page if it exists |
| self     | Url  | ✔        | Link to current page               |
| next     | Url  |          | Link to next page if it exists     |

JSON example:

```json
{
    "status": "ok",
    "payload": {
        "page_number": 12,
        "page_size": 10,
        "items": [
            {...},
            {...},
            {...}
        ],
        "links": {
            "prev": "https://api.contoso.com/v1.0/items?page=11&pagesize=10",
            "self": "https://api.contoso.com/v1.0/items?page=12&pagesize=10",
            "next": "https://api.contoso.com/v1.0/items?page=13&pagesize=10"
        }
    }
}
```

XML example:

```xml
<response>
    <staus>ok</staus>
    <payload>
        <page_number>12</page_number>
        <page_size>10</page_size>
        <items>
            <..>...</..>,
            <..>...</..>,
            <..>...</..>
        </items>
        <links>
            <prev>https://api.contoso.com/v1.0/items?page=11&pagesize=10</prev>
            <self>https://api.contoso.com/v1.0/items?page=12&pagesize=10</self>
            <next>https://api.contoso.com/v1.0/items?page=13&pagesize=10</next>
        </links>
    </payload>
</response>
```

## Collections

### Pagination

### Sorting

### Filtering

## Errors

ErrorResponse: Object

| Property | Type    | Required | Value   | Description                              |
|----------|---------|----------|---------|------------------------------------------|
| status   | String  | ✔        | "error" | message status                           |
| details  | Details | ✔        |         | Object containing more details of the error |

Details: Object

| Property | Type   | Required | Description                      |
|----------|--------|----------|----------------------------------|
| code     | String | ✔        | System error code                |
| message  | String | ✔        | System error message             |
| link     | Url    |          | ink to further error information |


```json
{
    "status": "error",
    "details": {
        "code": "InvalidPageNumber",
        "message": "Page number supplied is not a number",
        "link": "https://api.contoso.com/v1.0/errors/203"
    }
}
```

## Return Codes

## Security
