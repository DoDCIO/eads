# Introduction

# Conventions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be
interpreted as described in
[RFC2119](http://tools.ietf.org/html/rfc2119).

# Content Negotiation

## Client Responsibilities

Clients **MUST** send all data in request documents with the header `Content-Type: application/json`.

Clients **SHOULD** specify the preferred media type using one of the methods below:

### Query Parameter

Specify the preferred media type using the `format` query parameter.

```
https://[BASE_URL]/api/v1/[RESOURCE]?format=json
```

### URL Extension

Specify the preferred media type using a URL extension.

```
https://[BASE_URL]/api/v1/[RESOURCE].json
```

### Accept Header

Specify the preferred media type using the Accept request header.

```
Accept: application/json;
```

> The Accept header can accept multiple media types along with quality factors to specify media type preference. See [Section 14.1 of RFC2616](https://tools.ietf.org/html/rfc2616#section-14.1) for more information.

## Server Responsibilities

Servers **MUST** send all data in response documents with the header `Content-Type: application/json`.

Servers **MUST** respond with a `415 Unsupported Media Type` status code if a request specifies a `Content-Type` other than `Content-Type: application/json`.

Servers **MUST** respond with a `406 Not Acceptable` status code if an unsupported media type preference is specified.

Servers **SHOULD** use the following precedence to determine the appropriate media type for the response:

1. Query Parameter
2. URL Extension
3. Accept Header

> The first method found will be used. If no method is used to specify the preferred media type, json will be sent back by default.

# Document Structure

This section describes the structure of request/response documents.  These documents are defined in [JavaScript Object Notation (JSON)](https://tools.ietf.org/html/rfc7159).

## Top Level

A JSON object **MUST** be at the root of every request/response document.  This object defines a document's "top level".

A response document **MUST** contain at least one of the following top-level members:

* `meta`: a meta object containing information about the data being returned
* `data`: the resource(s) being returned
* `error`: an object containing information about any or all errors that occurred during the request

The members `data` and `error` **MUST NOT** coexist in the same document.

The `data` member **MUST** contain either:

* a single [resource object][resource objects] for requests targeting single resources
* an array of [resource objects] or an empty array for requests targeting resource collections

## <a href="#document-resource-objects" id="document-resource-objects" class="headerlink"></a> Resource Objects

A resource object **MUST** contain at least the following top-level members:

* `id`: The unique identifier for the resource [string].

In addition, a resource object **MAY** contain any of these top-level members:

* `createdAt`: The date/time the resource was created [ISO 8601 Datetime w/ Timezone].
* `updatedAt`: The date/time the resource was last updated [ISO 8601 Datetime w/ Timezone].

## Meta Objects

A meta object is used to provide additional information about the data being returned.

A meta object **MUST** contain at least the following top-level members:

* `resourceType`: The type of resource being returned in the `data` member [string].
* `responseTime`: The execution time of the request in milliseconds [integer].

In addition, a meta object **MAY** contain any of these top-level members:

* `user`: The user ID of the user making the request [string].
* `date`: The date/time the request was received [ISO 8601 Datetime w/ Timezone].  

# Retrieving Resources

Resources can be retrieved by sending a `GET` request to an endpoint.

## Partial Responses

## Sorting

A server **MAY** choose to support requests to sort resource collections based on one or more "sort fields".

An endpoint **MAY** support requests to sort the resources with a `sort` query parameter. The value for `sort` **MUST** represent sort fields.

```
GET /books?sort=name
```

An endpoint **MAY** support multiple sort fields by allowing comma-separated sort fields.  Sort fields **SHOULD** be applied in the order specified.

```
GET /books?sort=name,yearPublished
```

The sort order for each sort field **MUST** be ascending unless it is prefixed with a minus, in which case it **MUST** be descending.

```
GET /books?sort=name,-yearPublished
```
If the server does not support sorting as specified in the query parameter `sort`, it **MUST** return `400 Bad Request`.

## Pagination

## Filtering

# Creating, Updating, and Deleting Resources

# Query Parameters

# Errors

[resource objects]: #document-resource-objects
