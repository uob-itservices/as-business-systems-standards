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

Versioning should follow a date format [yyyy-MM-dd] and be included in a header [Api-Version]. For practicalities sake this does not need to be the release date of the API but can be related to the start of the development of that API version. Each released version the extends or superseeds an existings vesion of an API should have an greater date than any currently released versions.

## URL Structure

URLs should follow a common structure. The University Capability Model provides a structure that allows for APIs to be grouped logically, e.g:

* https://api.bham.ac.uk/academic/administration/curriculum/programmes-light
* https://api.bham.ac.uk/academic/student/assessment/feedback

For more details on this structure speak to data architecture.

## Supported methods / verbs

Operations MUST use the proper HTTP methods whenever possible, and operation idempotency MUST be respected.

Below is a list of methods that REST services SHOULD support. Not all resources will support all methods, but all resources using the methods below MUST conform to their usage.

| Method  | Description                                                  | Is Idempotent |
| ------- | ------------------------------------------------------------ | ------------- |
| GET     | Return the current value of an object                        | True          |
| PUT     | Replace an object, or create a named object, when applicable | True          |
| DELETE  | Delete an object                                             | True          |
| POST    | Create a new object based on the data provided, or submit a command | False         |
| HEAD    | Return metadata of an object for a GET response. Resources that support the GET method MAY support the HEAD method as well | True          |
| PATCH   | Apply a partial update to an object                          | False         |
| OPTIONS | Get information about a request; see below for details.      | True          |

## Standard request headers

| Header | Type         | Description                                                  |
| ------ | ------------ | ------------------------------------------------------------ |
| Accept | Content type | The requested content type for the response such as:<ul style="box-sizing: border-box; padding-left: 2em; margin-top: 0px; margin-bottom: 16px;"><li style="box-sizing: border-box;">application/xml</li><li style="box-sizing: border-box; margin-top: 0.25em;">application/json</li></ul>Per the HTTP guidelines, this is just a hint and responses MAY have a different content type, such as a blob fetch where a successful response will just be the blob stream as the payload. For services following OData, the preference order specified in OData SHOULD be followed. |

## Standard response headers

| Response Header | Required      | Description      |
| --------------- | ------------- | ---------------- |
| Content-Type    | All responses | The content type |

## Response format

In HTTP, response format SHOULD be requested by the client using the Accept header. This is a hint, and the server MAY ignore it if it chooses to. Clients MAY send multiple Accept headers and the service MAY choose one of them.

The default response format (no Accept header provided) SHOULD be application/json, and all services MUST support application/json.

Response: Object

| Property | Type    | Required | Value | Description                         |
| -------- | ------- | -------- | ----- | ----------------------------------- |
| status   | String  | ✔        | "ok"  | message status                      |
| payload  | Payload | ✔        |       | Object containing all returned data |

Payload: Object

| Property    | Type          | Required | Description                |
| ----------- | ------------- | -------- | -------------------------- |
| page_number | Number        | ✔        | Number of the current page |
| page_size   | Number        | ✔        | Length of the current page |
| has_more    | boolean       | ✔        | Indicates if there are more pages |
| items       | Array<Object> | ✔        | data returned              |
| links       | Links         |          | Navigation links           |

Links: Object

| Property | Type | Required | Description                        |
| -------- | ---- | -------- | ---------------------------------- |
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
        "has_more": true,
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
        <has_more>true</has_more>
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

Pagination is supported in the standard response format and MUST be supported in all APIs. The properties included in the return structure of `page_number`, `page_size` and `has_more` inform clients about where they are in the data set and if more data is available.

The pagenation object must be included in all data response payloads, even if there is only one or zero results in the returned collection.
If a result contains no items the status tag may show "no content"; however, the actual http status will not be "No Content" as the pagenation object is being returned.

Clients can request different pages of data by using query parameters:

| Parameter | Type | Default | Description                        |
| --------- | ---- | -------- | ---------------------------------- |
| page     | Query  | 1       | Specify page number to return |
| pagesize | Query  | 10      | Specify number of items to include in page |

If a page is requested and there is no data for that page to return the return value will have an empty items array.

### Sorting - Draft
TODO

### Filtering - Draft
TODO  

## Errors
Errors follow the approach detailed in [RFC7807 - Problem Details for HTTP APIs](https://datatracker.ietf.org/doc/html/rfc7807). This RFC documents a standard approach to communicating errors via APIs. An example of the JSON document that is returned is:

```
HTTP/1.1 403 Forbidden
Content-Type: application/problem+json
Content-Language: en

{
    "type": "https://example.com/probs/out-of-credit",
    "title": "You do not have enough credit.",
    "detail": "Your current balance is 30, but that costs 50.",
    "instance": "/account/12345/msgs/abc",
    "balance": 30,
    "accounts": ["/account/12345","/account/67890"],
    "status": 403
}
```

* type (string): URI that identifies the problem detail type. In this case “out-of-credit”.
* title (string): A short human-readable summary about the problem.
* detail (string): A human-readable explanation about the problem.
* status (number): HTTP Status Code.
* instance (string): A URI reference that identifies the specific occurrence of the problem.

For more details on this approach please refer to the RFC.

## Status Codes

Standard HTTP Status Codes MUST be used; see the HTTP Status Code definitions for more information.

## Documentation

APIs should be documented in line with the [OpenAPI Specification](http://spec.openapis.org/oas/v3.0.3).

## Security (Draft)

This is in draft status and may indicate OAuth 2.0 or OpenID Connect once a decision has been made.

## Products and Subscriptions

Products should be created to represent each end user systems of one or more APIs. This is to allow separate subscriptions to be created for use by these systems. This allows tracking of which system is using an api (which may be shared) and to what extent.

The preferred method for passing the subscription key is via header "Ocp-Apim-Subscription-Key".

It can also be passed as a query parameter "subscription-key".
