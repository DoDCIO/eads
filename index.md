---
layout: default
---

## <a href="#introduction" id="introduction" class="headerlink"></a> Introduction
The Enterprise APIs for Data Sharing (EADS) effort is defining a set of best practices and lessons learned for implementing web-based APIs (RESTful APIs) from existing DoD systems. This EADS Handbook contains recommended tactics, techniques, and procedures (TTPs) when making this digital transformation.

In the spirit of transparency and collaboration, the EADS team is working with the larger DoD Technical Community (in a [public GitHub repository](https://github.com/540co/eads)) to help define a solution that best fits the DoD's needs.  Please refer to the [CONTRIBUTING.md](./CONTRIBUTING.md) to see how you can contribute to this project.

### <a href="#what-is-rest" id="what-is-rest" class="headerlink"></a> What is REST?

*Coming soon...*

## <a href="#conventions" id="conventions" class="headerlink"></a> Conventions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be
interpreted as described in
[RFC2119](http://tools.ietf.org/html/rfc2119).

## <a href="#using-the-api" id="using-the-api" class="headerlink"></a> Using the API

All API access **MUST** be over HTTPS.

### <a href="#cors" id="cors" class="headerlink"></a> Cross-Origin Resource Sharing (CORS)

An important concept in web application security is the [same-origin policy](https://en.wikipedia.org/wiki/Same-origin_policy).  This policy allows scripts in one web page to access data in another web page only if they both have the same origin.  The origin is defined as the combination of URI scheme, hostname, and port number.

With the increasing popularity of JavaScript frameworks and heavier reliance on client-side (browser) applications, it becomes necessary to allow these apps to access resources from different origins... CORS to the rescue.

[Cross-origin resource sharing (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) allows restricted resources to be requested from a domain other than the domain from which the resource originated.

Servers **SHOULD** respond with the `Access-Control-Allow-Origin` header indicating which origins are allowed.

```http
Access-Control-Allow-Origin: http://www.example.com
```

A wildcard may be specified to indicate all origins are allowed:

```http
Access-Control-Allow-Origin: *
```

In addition to specifying the allowed origins, servers **SHOULD** specify allowed HTTP methods and HTTP headers as follows:

To allow `GET` `POST` `PUT` and `DELETE` methods, the following header would be specified:

```http
Access-Control-Allow-Methods: GET, POST, PUT, DELETE
```

To allow `Content-Type`, `Accept`, and `Authorization` headers, the following header would be specified:

```http
Access-Control-Allow-Headers: Content-Type, Accept, Authorization
```

A wildcard may be specified to allow all headers:

```http
Access-Control-Allow-Headers: *
```

A server **MAY** specify additional CORS headers.

### <a href="#content-negotiation" id="content-negotiation" class="headerlink"></a> Content Negotiation

Clients **MUST** send data as JSON.  XML **MUST NOT** be used.

Servers **SHOULD** return data using JSON.  Alternative formats, such as PDF and CSV **MAY** be allowed.  XML **MUST NOT** be used.

Clients and Servers **MUST** send all data in request/response documents with the header:

```http
Content-Type: application/json
```

Servers **MUST** respond with a `415 Unsupported Media Type` status code if a client specifies a `Content-Type` other than `application/json`.

Clients **SHOULD** specify a response media type of JSON with the header:

```http
Accept: application/json
```

> If the `Accept` header is not sent, JSON will be sent back by default.

Servers **MUST** respond with a `406 Not Acceptable` status code if a client requests an unsupported response format.

### <a href="#versioning" id="versioning" class="headerlink"></a> Versioning

Servers **MUST** version their APIs.  Version names **MUST** be whole number integers preceded by the letter v: `v1`, `v2`, `v3`...

Clients **MUST** specify the desired version of the API as part of the request path: `https://[BASE_URL]/[VERSION]/[RESOURCE]`

```
https://api.example.com/v1/books
```

Servers **MUST** respond with a `406 Not Acceptable` status code if a request specifies an unsupported version.

Servers **MUST** increment the version number any time breaking changes are introduced.

### <a href="#authentication" id="authentication" class="headerlink"></a> Authentication

Servers **SHOULD** use OAuth 2.0 [RFC 6749](https://tools.ietf.org/html/rfc6749) for API authentication.

### <a href="#documentation" id="documentation" class="headerlink"></a> Documentation

Servers **SHOULD** use the [OpenAPI Specification (fka The Swagger Specification)](https://github.com/OAI/OpenAPI-Specification) to document the API.

Servers **SHOULD** place this documentation at the root path of the API.  For example, documentation for version 1 of an API should be located at: `https://[BASE_URL]/v1`

## <a href="#schema" id="schema" class="headerlink"></a> Schema

This section describes the structure of request/response documents.  These documents are defined in [JavaScript Object Notation (JSON)](https://tools.ietf.org/html/rfc7159).

All timestamps are returned in ISO 8601 format:

```
YYYY-MM-DDTHH:MM:SSZ
```

### <a href="#top-level" id="top-level" class="headerlink"></a> Top Level

A JSON object **MUST** be at the root of every request/response document.  This object defines a document's "top level".

A document **MUST** contain at least one of the following top-level members:

* `meta`: a [meta object][meta objects] containing information about the data being returned.
* `data`: the resource(s) being sent/returned.
* `error`: an [error object][error objects] containing information about any or all errors that occurred during the request.

The members `data` and `error` **MUST NOT** coexist in the same document.

The `data` member **MUST** contain either:

* a single [resource object][resource objects] for requests targeting single resources
* an array of [resource objects] or an empty array for requests targeting resource collections

### <a href="#resource-objects" id="resource-objects" class="headerlink"></a> Resource Objects

A resource object **MUST** contain at least the following top-level members:

* `id`: The unique identifier for the resource [string].
* `href`: The unique url for the resource [string].

In addition, a resource object **MAY** contain any of these top-level members:

* `createdAt`: The timestamp the resource was created [string (ISO 8601)].
* `updatedAt`: The timestamp the resource was last updated [string (ISO 8601)].

### <a href="#meta-objects" id="meta-objects" class="headerlink"></a> Meta Objects

A meta object is used to provide additional information about the data being returned.

A meta object **MUST** contain at least the following top-level members:

* `resourceType`: The type of resource being returned in the `data` member [string].
* `responseTime`: The execution time of the request in milliseconds [integer].

In addition, a meta object **MAY** contain any of these top-level members:

* `user`: The user ID of the user making the request [string].
* `date`: The date/time the request was received [ISO 8601 Datetime w/ Timezone].

### <a href="#summary-representations" id="summary-representations" class="headerlink"></a> Summary Representations

When retrieving a list of [resource objects], the response will include a *subset* of the attributes for that resource.  This is the "summary" representation of the resource.  To obtain all attributes for a resource, retrieve the "detailed" representation.

```http
GET /books HTTP/1.1
Accept: application/json

{
  "meta": {},
  "data": [
    {
      "id": "1",
      "href": "/books/1",
      "title": "The Greatest Book in the World"
    },
    {
      "id": "2",
      "href": "/books/2",
      "title": "The Worst Book in the World"
    }
  ]
}
```

### <a href="#detailed-representations" id="detailed-representations" class="headerlink"></a> Detailed Representations

When retrieving an individual resource, the response will typically include *all* attributes for that resource.  This is the "detailed" representation of the resource.

```http
GET /books/1 HTTP/1.1
Accept: application/json

{
  "meta": {},
  "data": {
    "id": "1",
    "href": "/books/1",
    "title": "The Greatest Book in the World",
    "author": {
      "id": "247",
      "href": "/authors/247",
      "name": "John Smith"
    },
    "publisher": {
      "id": "38",
      "href": "/publishers/38",
      "name": "Only the Greatest, Inc."
    },
    "yearPublished": "2016",
    "reviews": {
      "href": "/books/1/reviews",
      "totalCount": 382
    }
  }
}
```

Sub-resources included in a detailed representation **MUST** use the summary representation

### <a href="#nested-representations" id="nested-representations" class="headerlink"></a> Nested Collection Representations

To-Many relationships, when included in a detailed representation, **MUST** be an object with the following required fields:

* `href`: The url for the nested collection [string].
* `totalCount`: The total number of items in the collection [integer].

For example, when including reviews for a book, the reviews relationship would be included as follows:

```http
GET /books/1 HTTP/1.1
Accept: application/json

{
  "meta": {},
  "data": {
    "id": "1",
    "href": "/books/1",
    "title": "The Greatest Book in the World",
    "reviews": {
      "href": "/books/1/reviews",
      "totalCount": 382
    }
  }
}
```

## <a href="#retrieving-data" id="retrieving-data" class="headerlink"></a> Retrieving Data

Data can be retrieved by sending a `GET` request to an endpoint.

Responses can be further refined with the optional features described below.

### <a href="#retrieving-resources" id="retrieving-resources" class="headerlink"></a> Retrieving Resources

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
      "href": "/books/1",
      "title": "The Greatest Book in the World"
    },
    {
      "id": "2",
      "href": "/books/2",
      "title": "The Worst Book in the World"
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
    "href": "/books/1",
    "title": "The Greatest Book in the World",
    "author": {
      "id": "247",
      "href": "/authors/247",
      "name": "John Smith"
    },
    "publisher": {
      "id": "38",
      "href": "/publishers/38",
      "name": "Only the Greatest, Inc."
    },
    "yearPublished": "2016",
    "reviews": {
      "href": "/books/1/reviews",
      "totalCount": 382
    }
  }
}
```

##### 404 Not Found

A server **MUST** respond with `404 Not Found` when processing a request to retrieve a single resource that does not exist.

##### Other Responses

A server **MAY** respond with other HTTP status codes.

A server **MAY** include error details with error responses.

### <a href="#partial-resources" id="partial-resources" class="headerlink"></a> Partial Resources

A client **MAY** request that an endpoint return only specific fields in the response by including a `fields` query parameter.

The value of the `fields` parameter **MUST** be a comma-separated list that refers to the name(s) of the fields to be returned.

To return a subset of fields for nested resources, use dot notation for the field names.  For example, to return only the name of an author, specify the field name as `author.name`.

If a client requests a specific set of fields using the `fields` query parameter, an endpoint **MUST** include only the mandatory and specified fields.  Additional fields **MUST NOT** be included in the response.

A client **MUST** only specify fields available in the detailed representation for a resource.

A server **MUST** respond with `400 Bad Request` when provided invalid or unavailable field names.

```http
GET /books?fields=name,yearPublished HTTP/1.1
Accept: application/json
```

### <a href="#sorting" id="sorting" class="headerlink"></a> Sorting

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

To specify fields for nested resources, use dot notation for the field names.  For example, to sort by the name of an author, specify the sort field name as `author.name`.

A client **MUST** only specify sort fields available in the detailed representation for a resource.

If the server does not support sorting as specified in the query parameter `sort`, it **MUST** return `400 Bad Request`.

### <a href="#pagination" id="pagination" class="headerlink"></a> Pagination

A server **MAY** choose to limit the number of resources returned in a response.

A server **MUST** provide links to traverse a paginated data set (“pagination links”).  These links **MUST** be provided using the `Link` header as defined in [RFC5988](https://tools.ietf.org/html/rfc5988).

The following links **MUST** be used for pagination:

* `first`: The first page of data
* `last`: The last page of data
* `prev`: The previous page of data
* `next`: The next page of data

An example `Link` header might look like this

```http
Link: <https://[BASE_URL]/v1/[RESOURCE]/?limit=20&offset=0>; rel="first",
      <https://[BASE_URL]/v1/[RESOURCE]/?limit=20&offset=380>; rel="last",
      <https://[BASE_URL]/v1/[RESOURCE]/?limit=20&offset=40>; rel="prev",
      <https://[BASE_URL]/v1/[RESOURCE]/?limit=20&offset=80>; rel="next"
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

Servers **MUST** define `default` and `maximum` values for `limit`.

Servers **MUST** return a `400 Bad Request` if the value specified by the `offset` query parameter exceeds the total resource count of the resource being retrieved.

### <a href="#filtering" id="filtering" class="headerlink"></a> Filtering

A server **MAY** choose to support requests to filter resource collections based on one or more criterion.

An endpoint **MAY** support requests to filter the resources with a `filters` query parameter.

#### Single Conditions

Single filters are implemented by providing the name of the field on which to filter followed by the operator on which to test and lastly the value or values on which to test against. The list of valid operators are as follows.

Operator | Description | URL Encoded Operator | Examples
-------- | ----------- | -------------------- | --------
== | equal | %3D%3D | The year published is '2016'<br /> `?filters=yearPublished%3D%3D2016`
!= | not equal | !%3D | The year published is not '2016'<br /> `filters=yearPublished!%3D2016`
> | greater than | %3E | The year published is greater than 2000<br /> `?filters=yearPublished%3E2000`
< | less than | %3C | The year published is less than 2016<br /> `?filters=yearPublished%3C2016`
>= | greater than or equal | %3E%3D | The year published is greater than or equal to 2000<br /> `?filters=yearPublished%3E%3D2000`
<= | less than or equal | %3C%3D | The year published is less than or equal to 2016<br /> `?filters=yearPublished%3C%3D2000`
>=< | inclusively between | %3E%3D%3C | The year published is at least 2000 and at most 2016<br /> `?filters=yearPublished%3E%3D%3C2000;2016`
>< | exclusively between | %3E%3C | The year published is greater than 2000 and less than 2016<br /> `?filters=yearPublished%3E%3C2000;2016`

#### Multiple Conditions

Multiple conditions will be accommodated by separating single conditions with a comma (',').

```
[RESOURCE_URL]?filters=attribute%3D%3Dvalue,attribute2%3E%3CminValue;maxValue
```

To specify fields for nested resources, use dot notation for the field names.  For example, to filter using the name of an author, specify the filter field name as `author.name`.

A client **MUST** only specify filter fields available in the detailed representation for a resource.

#### Special Characters

As the comma and semi-colon are assigned special meaning, any instance of the following characters contained within individual values must be escaped.

Character | Text | Escaped As
--------- | ---- | ----------
semicolon | ; | \;
comma | , | \,
backslash | \ | \\\

```
[RESOURCE_URL]?filters=fullName%3D%3DMontoya\,Inigo
```

## <a href="#cud-resources" id="cud-resources" class="headerlink"></a> Creating, Updating, and Deleting Resources

A server **MAY** allow resources to be created.  It **MAY** also allow existing resources to be modified or deleted.

A request **MUST** completely succeed or fail (in a single "transaction").  No partial updates are allowed.

### <a href="#creating-resources" id="creating-resources" class="headerlink"></a> Creating Resources

A resource can be created by sending a `POST` request to the collections endpoint for that resource type.  The request **MUST** include a single [resource object][resource objects].

A new book might be created with the following request:

```http
POST /books HTTP/1.1
Content-Type: application/json
Accept: application/json

{
  "data": {
    "title": "The 2nd Greatest Book in the World",
    "author": {
      "id": "175"
    },
    "publisher": {
      "id": "19"
    },
    "yearPublished": "2016"
  }
}
```

#### Responses

##### 201 Created

If the requested resource has been created successfully, the server **MUST** return a `201 Created` status code.

The response **SHOULD** include a `Location` header identifying the location of the newly created resource.

The response **MUST** also include a document that contains the newly created resource.

```http
HTTP/1.1 201 CREATED
Location: https://api.example.com/v1/books/3
Content-Type: application/json

{
  "meta": {
    "resourceType": "Book",
    "responseTime": 63
  },
  "data": {
    "id": "3",
    "href": "/books/3",
    "title": "The 2nd Greatest Book in the World",
    "author": {
      "id": "175",
      "href": "/authors/175",
      "name": "Jane Smith"
    },
    "publisher": {
      "id": "19",
      "href": "/publishers/19",
      "name": "We Settle for Less, Inc."
    },
    "yearPublished": "2016",
    "reviews": {
      "href": "/books/3/reviews",
      "totalCount": 0
    }
  }
}
```

##### 400 Bad Request

A server **MAY** return `400 Bad Request` if unable to create the resource due to missing or invalid data.

##### 403 Forbidden

A server **MAY** return `403 Forbidden` in response to an unsupported request to create a resource.

##### Other Responses

A server **MAY** respond with other HTTP status codes.

### <a href="#updating-resources" id="updating-resources" class="headerlink"></a> Updating Resources

A resource can be updated by sending a `PATCH` request to the URL that represents the resource.

The `PATCH` request **MUST** include a single [resource object][resource objects].

```http
PATCH /books/1 HTTP/1.1
Content-Type: application/json
Accept: application/json

{
  "data": {
    "title": "The Best Book in the World"
  }
}
```

#### Updating a Resource's Attributes

Any or all of a resource's attributes **MAY** be included in the resource object included in a `PATCH` request.

If a request does not include all of the attributes for a resource, the server **MUST** interpret the missing attributes as if they were included with their current values.  The server **MUST NOT** interpret missing attributes as `null` values.

#### Updating a Resource's Relationships

Any or all of a resource's relationships **MAY** be included in the resource object included in a `PATCH` request.

If a request does not include all of the relationships for a resource, the server **MUST** interpret the missing relationships as if they were included with their current values.  The server **MUST NOT** interpret missing relationships as `null` or empty values.

If a relationship is included in the resource object included in a `PATCH` request, the relationship's value will be replaced with the value specified in the request.  A complete replacement will be performed on to-many relationships.

A server **MAY** reject an attempt to do a full replacement of a to-many relationship.  If so, the server **MUST** reject the entire update, and return a `403 Forbidden` response.

#### Responses

##### 200 OK

A server **MUST** return a `200 OK` status code if an update is successful.  The response document **MUST** include a representation of the updated resource as if a `GET` request was made.

##### 400 Bad Request

A server **MAY** return `400 Bad Request` if unable to update the resource due to missing or invalid data.

##### 403 Forbidden

A server **MAY** return `403 Forbidden` in response to an unsupported request to update a resource.

##### 404 Not Found

A server **MUST** return `404 Not Found` when processing a request to modify a resource (or related resource) that does not exist.

##### Other Responses

A server **MAY** respond with other HTTP status codes.

### <a href="#updating-relationships" id="updating-relationships" class="headerlink"></a> Updating Relationships

Although relationships can be modified along with resources, the server **MAY** provide links to modify them independently.

#### Updating To-One Relationships

If the server allows independent modification of relationships...

A server **MUST** respond to `PATCH` requests for the to-one relationship URL.

The `PATCH` request **MUST** include a top-level member named `data` containing one of the following:

* a resource identifier object corresponding to the new related resource.
* `null`, to remove the relationship.

A request to update the author of a book:

```http
PATCH /books/1/author HTTP/1.1
Content-Type: application/json
Accept: application/json

{
  "data": {
    "id": "339"
  }
}
```

A request to clear the author of a book:

```http
PATCH /books/1/author HTTP/1.1
Content-Type: application/json
Accept: application/json

{
  "data": null
}
```

If the relationship is updated successfully then the server **MUST** return a successful response.

#### Updating To-Many Relationships

If the server allows independent modifications of relationships...

A server **MUST** respond to `PATCH`, `POST`, and `DELETE` requests for the to-many relationship URL.

For all request types, the body **MUST** contain a `data` member whose value is an empty array or an array of resource identifier objects.

If a client makes a `PATCH` request, the server **MUST** either completely replace every member of the relationship, return an appropriate error response if some resources can not be found or accessed, or return a `403 Forbidden` response if complete replacement is not allowed by the server.

The following request replaces every tag for a book:

```http
PATCH /books/1/tags HTTP/1.1
Content-Type: application/json
Accept: application/json

{
  "data": [
    { "id": "13" },
    { "id": "43" }
  ]
}
```

The following request clears every tag for a book:

```http
PATCH /books/1/tags HTTP/1.1
Content-Type: application/json
Accept: application/json

{
  "data": []
}
```

If a client makes a `POST` request, the server **MUST** add the specified members to the relationship unless they are already present.  If a given member is already in the relationship, the server **MUST NOT** add it again.

If all of the specified resources can be added to, or are already present in, the relationship then the server **MUST** return a successful response.

The following request adds the tag with ID `345` to the list of tags for the book with ID `1`:

```http
POST /books/1/tags HTTP/1.1
Content-Type: application/json
Accept: application/json

{
  "data": [
    { "id": "345" }
  ]
}
```

If the client makes a `DELETE` request, the server **MUST** delete the specified members from the relationship or return a `403 Forbidden` response.  If all of the specified members resources are able to be removed from, or are already missing from, the relationship then the server **MUST** return a successful response.

The following request removes tags with IDs `17` and `29` from the book with ID `1`:

```http
DELETE /books/1/tags HTTP/1.1
Content-Type: application/json
Accept: application/json

{
  "data": [
    { "id": "17" },
    { "id": "29" }
  ]
}
```

#### Responses

##### 200 OK

A server **MUST** return a `200 OK` status code if an update is successful.  The response document **MUST** include a representation of the updated relationship(s).

##### 403 Forbidden

A server **MUST** return `403 Forbidden` in responses to an unsupported request to update a relationship.

##### Other Responses

A server **MAY** respond with other HTTP status codes.

### <a href="#deleting-resources" id="deleting-resources" class="headerlink"></a> Deleting Resources

An individual resource can be deleted by making a `DELETE` request to the resource's URL:

```http
DELETE /books/1 HTTP/1.1
Accept: application/json
```

#### Responses

##### 204 No Content

A server **MUST** return a `204 No Content` status code if a deletion request is successful and no content is returned.

##### Other Responses

A server **MAY** respond with other HTTP status codes.

## <a href="#query-parameters" id="query-parameters" class="headerlink"></a> Query Parameters

If a server encounters a query parameter that it does not know how to process, it **MUST** return `400 Bad Request`

## <a href="#errors" id="errors" class="headerlink"></a> Errors

### <a href="#processing-errors" id="processing-errors" class="headerlink"></a> Processing Errors

A server **MAY** choose to stop processing as soon as a problem is encountered, or it **MAY** continue processing and encounter multiple problems.

When a server encounters multiple problems for a single request, the most generally applicable HTTP error code **SHOULD** be used in the response. The following is a list of common error codes:

Status Code | Status Message | Description
----------- | -------------- | -----------
400 | BAD REQUEST | Request failed. This is mostly due to missing or invalid request parameters.
401 | UNAUTHORIZED | Request failed. This status code is used for missing or invalid credentials (NOT AUTHENTICATED).
403 | FORBIDDEN | Request failed. This status code is used for insufficient privileges (NOT AUTHORIZED).
404 | NOT FOUND | Request failed. This status code is used when the requested resource does not exist.
405 | METHOD NOT ALLOWED | Request failed. This status code is used when the request is known but not supported by the target resource.
500 | INTERNAL SERVER ERROR | Request failed. An error occurred on the server.

### <a href="#error-objects" id="error-objects" class="headerlink"></a> Error Objects

Error objects provide additional information about problems encountered while processing a request.

An error object **MUST** contain at least the following top-level members:

* `developerMessage`: Provides the developer with information about the error(s) [string].
* `errorCode`: Application specific error code [string].

In addition, a resource object **MAY** contain any of these top-level members:

* `userMessage`: Informational message for end-users [string].
* `moreInfo`: Link to find additional information about the error [string].

An example error object:

```json
{
  "error": {
    "developerMessage" : "A detailed description of the problem with suggestions on how to solve.",
    "userMessage" : "An informational message for end-users.",
    "errorCode" : 9583,
    "moreInfo" : "https://link/to/additional/information"
  }
}
```

[resource objects]: #resource-objects
[meta objects]: #meta-objects
[error objects]: #error-objects
