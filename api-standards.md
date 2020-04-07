# API Standards
## 1. Introduction

These standards shamelessly borrow from the following sources with content from both being repeated here:

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

This document does not pretend to cover every possible eventuality. If use cases are found which are missing from this standard, the approach must be agreed and added. The same is true for any practices detailed here that prove not fit for purpose. They should be updated to a more appropriate approach.

## Conventions

All services MUST return [JavaScript Object Notation (JSON)](https://en.wikipedia.org/wiki/JSON) by default. [Extensible Markup Language (XML)](https://en.wikipedia.org/wiki/XML) MAY be available if specifically requested via an Accept header.

## Versioning 

All APIs MUST be versioned. Services are versioned using a Major.Minor versioning scheme. The version of the API is embedded in the path of the request URL, at the end of the service root, e.g.:

* `https://api.contoso.com/v1.0/products/users`

## URL Structure

TODO

## Supported methods / verbs

Operations MUST use the proper HTTP methods whenever possible, and operation idempotency MUST be respected.

Below is a list of methods that REST services SHOULD support. Not all resources will support all methods, but all resources using the methods below MUST conform to their usage.

| Method  | Description                              | Is Idempotent |
|---------|------------------------------------------|---------------|
| GET     | Return the current value of an object    | True          |
| PUT     | Replace an object, or create a named object, when applicable | True          |
| DELETE  | Delete an object                         | True          |
| POST    | Create a new object based on the data provided, or submit a command | False         |
| HEAD    | Return metadata of an object for a GET response. Resources that support the GET method MAY support the HEAD method as well | True          |
| PATCH   | Apply a partial update to an object      | False         |
| OPTIONS | Get information about a request; see below for details. | True          |

## Standard request headers

| Header | Type         | Description                              |
|--------|--------------|------------------------------------------|
| Accept | Content type | The requested content type for the response such as:<ul style="box-sizing: border-box; padding-left: 2em; margin-top: 0px; margin-bottom: 16px;"><li style="box-sizing: border-box;">application/xml</li><li style="box-sizing: border-box; margin-top: 0.25em;">application/json</li></ul>Per the HTTP guidelines, this is just a hint and responses MAY have a different content type, such as a blob fetch where a successful response will just be the blob stream as the payload. For services following OData, the preference order specified in OData SHOULD be followed. |

## Standard response headers

| Response Header | Required      | Description                              |
|-----------------|---------------|------------------------------------------|
| Content-Type    | All responses | The content type                         |

## Response format

In HTTP, response format SHOULD be requested by the client using the Accept header. This is a hint, and the server MAY ignore it if it chooses to. Clients MAY send multiple Accept headers and the service MAY choose one of them.

The default response format (no Accept header provided) SHOULD be application/json, and all services MUST support application/json.

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
TODO

### Sorting
TODO

### Filtering
TODO  

## Errors

The error response MUST be a single JSON object. This object MUST have a property status with the value "error." The value MUST be a JSON object.

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
| link     | Url    |          | Link to further error information |


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

## Status Codes

Standard HTTP Status Codes SHOULD be used; see the HTTP Status Code definitions for more information.

## Documentation

APIs should be documented in line with the [OpenAPI Specification](http://spec.openapis.org/oas/v3.0.3).
