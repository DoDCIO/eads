## Introduction

## Conventions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be
interpreted as described in
[RFC2119](http://tools.ietf.org/html/rfc2119).

## Schema

All API access is over HTTPS and all data is sent and received as JSON.

All timestamps are returned in ISO 8601 format:

```
YYYY-MM-DDTHH:MM:SSZ
```

## Content Negotiation

### Client Responsibilities

Clients **MUST** send all data in request documents with the header `Content-Type: application/json`.

Clients **SHOULD** specify a response media type of JSON using the header: `Accept: application/json;`

> If the `Accept` header is not sent, JSON will be sent back by default.

### Server Responsibilities

Servers **MUST** send all data in response documents with the header `Content-Type: application/json`.

Servers **MUST** respond with a `415 Unsupported Media Type` status code if a request specifies a `Content-Type` other than `Content-Type: application/json`.

Servers **MUST** respond with a `406 Not Acceptable` status code if a request specifies `Accept` other than `Accept: application/json;`.

## Document Structure

This section describes the structure of request/response documents.  These documents are defined in [JavaScript Object Notation (JSON)](https://tools.ietf.org/html/rfc7159).

### Top Level

A JSON object **MUST** be at the root of every request/response document.  This object defines a document's "top level".

A response document **MUST** contain at least one of the following top-level members:

* `meta`: a meta object containing information about the data being returned
* `data`: the resource(s) being returned
* `error`: an object containing information about any or all errors that occurred during the request

The members `data` and `error` **MUST NOT** coexist in the same document.

The `data` member **MUST** contain either:

* a single [resource object][resource objects] for requests targeting single resources
* an array of [resource objects] or an empty array for requests targeting resource collections

### <a href="#document-resource-objects" id="document-resource-objects" class="headerlink"></a> Resource Objects

A resource object **MUST** contain at least the following top-level members:

* `id`: The unique identifier for the resource [string].

In addition, a resource object **MAY** contain any of these top-level members:

* `createdAt`: The date/time the resource was created [ISO 8601 Datetime w/ Timezone].
* `updatedAt`: The date/time the resource was last updated [ISO 8601 Datetime w/ Timezone].

### Meta Objects

A meta object is used to provide additional information about the data being returned.

A meta object **MUST** contain at least the following top-level members:

* `resourceType`: The type of resource being returned in the `data` member [string].
* `responseTime`: The execution time of the request in milliseconds [integer].

In addition, a meta object **MAY** contain any of these top-level members:

* `user`: The user ID of the user making the request [string].
* `date`: The date/time the request was received [ISO 8601 Datetime w/ Timezone].  

## Retrieving Data

Data can be retrieved by sending a `GET` request to an endpoint.

Responses can be further refined with the optional features described below.

### Retrieving Resources

There are 2 ways to retrieve resources:

* Retrieve a collection of resources
* Retrieve a single resource

The following request retrieves a collection of books:

```http
GET /books HTTP/1.1
Accept: application/json
```

The following request retrieves a single book:

```http
GET /books/1 HTTP/1.1
Accept: application/json
```

#### Responses

##### 200 OK

A server **MUST** respond to a successful request to retrieve an individual resource or resource collection with a `200 OK` response.

A server **MUST** respond to a successful request to retrieve a resource collection with an array of [resource objects] or an empty array (`[]`).

A `GET` request to a collection of books could return:

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "meta": {
    "resourceType": "Book",
    "responseTime": 73
  },
  "data": [
    {
      "id": "1",
      "name": "The Best Book Ever",
      "yearPublished": "2005"
    },
    {
      "id": "2",
      "name": "Another Book",
      "yearPublished": "2012"
    }
  ]
}
```

A similar response representing an empty collection would be:

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "meta": {
    "resourceType": "Book",
    "responseTime": 26
  },
  "data": []
}
```

A `GET` request to an individual book could return:

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "meta": {
    "resourceType": "Book",
    "responseTime": 49
  },
  "data": {
    "id": "1",
    "name": "The Best Book Ever",
    "yearPublished": "2005"
  }
}
```
##### 404 Not Found

A server **MUST** respond with `404 Not Found` when processing a request to retrieve a single resource that does not exist.

##### Other Responses

A server **MAY** respond with other HTTP status codes.

A server **MAY** include error details with error responses.

### Partial Responses

A client **MAY** request that an endpoint return only specific fields in the response by including a `fields` query parameter.

The value of the `fields` parameter **MUST** be a comma-separated list that refers to the name(s) of the fields to be returned.

If a client requests a specific set of fields using the `fields` query parameter, an endpoint **MUST NOT** include additional fields in resource objects in its response.

```http
GET /books?fields=name,yearPublished HTTP/1.1
Accept: application/json
```

### Sorting

A server **MAY** choose to support requests to sort resource collections based on one or more "sort fields".

An endpoint **MAY** support requests to sort the resources with a `sort` query parameter. The value for `sort` **MUST** represent sort fields.

```http
GET /books?sort=name HTTP/1.1
Accept: application/json
```

An endpoint **MAY** support multiple sort fields by allowing comma-separated sort fields.  Sort fields **SHOULD** be applied in the order specified.

```http
GET /books?sort=name,yearPublished HTTP/1.1
Accept: application/json
```

The sort order for each sort field **MUST** be ascending unless it is prefixed with a minus, in which case it **MUST** be descending.

```http
GET /books?sort=name,-yearPublished HTTP/1.1
Accept: application/json
```
If the server does not support sorting as specified in the query parameter `sort`, it **MUST** return `400 Bad Request`.

### Pagination

A server **MAY** choose to limit the number of resources returned in a response.

A server **MUST** provide links to traverse a paginated data set (“pagination links”).  These links **MUST** be provided using the `Link` header as defined in [RFC5988](https://tools.ietf.org/html/rfc5988).

The following links **MUST** be used for pagination:

* `first`: The first page of data
* `last`: The last page of data
* `prev`: The previous page of data
* `next`: The next page of data

An example `Link` header might look like this

```http
Link: <https://[BASE_URL]/api/v1/[RESOURCE]/?limit=20&offset=0>; rel="first",
      <https://[BASE_URL]/api/v1/[RESOURCE]/?limit=20&offset=380>; rel="last",
      <https://[BASE_URL]/api/v1/[RESOURCE]/?limit=20&offset=40>; rel="prev",
      <https://[BASE_URL]/api/v1/[RESOURCE]/?limit=20&offset=80>; rel="next"
```

A link **MUST** be omitted if it is unavailable.

The `limit` and `offset` query parameters are reserved for pagination and **SHOULD** be used by servers and clients for pagination operations.

* `limit`: The number of resource objects to return in the response
* `offset`: The location in the overall resultset to begin retrieving resource objects

An example client request using `limit` and `offset`

```http
GET /books?limit=20&offset=40 HTTP/1.1
Accept: application/json
```

Servers **MUST** return a `400 Bad Request` if the value specified by the `offset` query parameter exceeds the total resource count of the resource being retrieved.

### Filtering

## Creating, Updating, and Deleting Resources

## Query Parameters

## Errors

[resource objects]: #document-resource-objects
