---
layout: default
---

# Enterprise APIs for Data Sharing (EADS) Handbook

## <a href="#introduction" id="introduction" class="headerlink"></a> Introduction
The Enterprise APIs for Data Sharing (EADS) effort defines a set of guidelines for developing Department of Defense (DoD) APIs - encouraging consistency, maintainability, and best practices across applications. This EADS Handbook contains recommended tactics, techniques, and procedures (TTPs) to help balance RESTful API interfaces with a positive developer experience (DX).

This document borrows heavily from:

* [White House Web API Standards](https://github.com/whitehouse/api-standards)
* [18F API Standards](https://github.com/18F/api-standards)
* [Roy Fielding's Dissertation on REST](http://www.ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm)
* [{json:api}](http://jsonapi.org)

In the spirit of transparency and collaboration, the EADS team is working with the larger DoD Technical Community (in a [public GitHub repository](https://github.com/540co/eads)) to help define a solution that best fits the DoD's needs.  Please refer to the [CONTRIBUTING.md](https://github.com/540co/eads/CONTRIBUTING.md) to see how you can contribute to this project.

<hr/>

## <a href="#conventions" id="conventions" class="headerlink"></a> Conventions
Please note that, while RFC-style conventions are used throughout this document for clarity to the development community, the content herein is not currently published as an official DoD instruction, and should be interpreted as recommended best practices only.  

In the context of following API development best practices, the key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be
interpreted as described in
[RFC2119](http://tools.ietf.org/html/rfc2119).

<hr/>

## <a href="#what-is-rest" id="what-is-rest" class="headerlink"></a> What is REST?

[Representational State Transfer (REST)](https://en.wikipedia.org/wiki/Representational_state_transfer) is a network-based architectural style for distributed hypermedia systems.  It was introduced by [Roy Fielding](https://en.wikipedia.org/wiki/Roy_Fielding) in his 2000 PhD dissertation ["Architectural Styles and the Design of Network-based Software Architectures"](http://www.ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm).

A RESTful system is defined by six guiding constraints which restrict how a server processes and responds to client requests.  These constraints are intended to gain performance, scalability, simplicity, modifiability, visibility, portability, and reliability.  The six constraints are:

* **Client-Server** - Separation of concerns (user interface vs data storage).
* **Stateless** - Each request must contain all of the information necessary to understand and process the request.
* **Cacheable** - Data within a response must be labeled as cacheable or non-cacheable in order to improve network efficiency.
* **Layered System** - hierarchical layers constraining component behavior
* **Uniform Interface** - Identification of resources; manipulation of resources through representations; self-descriptive messages; and, hypermedia as the engine of application state (HATEOS)
* **Code on Demand** - Ability to download and execute code.

In addition to the benefits listed perviously, REST leverages the HTTP protocol making it a desired architectural style due to simplicity and ease of implementation.

> **NOTE**: REST is **NOT** a standard or protocol.  It is an architectural style/pattern.

<hr/>

## <a href="#restful-api-guidelines" id="restful-api-guidelines" class="headerlink"></a> RESTful API Guidelines

This section describes general guidelines for RESTful APIs.

<hr/>

### <a href="#https" id="https" class="headerlink"></a> Use HTTPS

Published APIs **MUST** use and require HTTPS (using TLS/SSL). HTTPS provides:

* **Security**. The contents of the request are encrypted across the Internet.
* **Authenticity**. A stronger guarantee that a client is communicating with the real API.
* **Privacy**. Enhanced privacy for apps and users using the API. HTTP headers and query string parameters (among other things) will be encrypted.
* **Compatibility**. Broader client-side compatibility. For CORS requests to the API to work on HTTPS websites -- to not be blocked as mixed content -- those requests must be over HTTPS.

HTTPS **SHOULD** be configured using guides provided by [DISA](http://iase.disa.mil/pki-pke/Pages/admin.aspx).

> **NOTE** HTTP **MAY** be used for non-published, purely internal interfaces.

<hr/>

### <a href="#date-format" id="date-format" class="headerlink"></a> Use a consistent date format

* The ISO 8601 date/time format **MUST** be used `YYYY-MM-DDTHH:MM:SSZ`.
* Maintain all times in UTC.
* This date format is used all over the web, and puts each field in consistent order -- from least granular to most granular.

Example date:

```
2013-02-27
```

Example date with time:

```
2013-02-27-T10:00:00Z
```

<hr/>

### <a href="#json" id="json" class="headerlink"></a> Use JSON

[JSON](https://en.wikipedia.org/wiki/JSON) is an excellent, widely supported transport format, suitable for many web APIs.

Supporting JSON and only JSON is a practical default for APIs, and generally reduces complexity for both the API provider and consumer.

General JSON guidelines:

* Responses **MUST** be a **JSON object** (not an array). Using an array to return results limits the ability to include metadata about results, and limits the API's ability to add additional top-level keys in the future.
* **Don't use unpredictable keys**. Parsing a JSON response where keys are unpredictable (e.g. derived from data) is difficult, and adds friction for clients.
* **Use consistent case for keys**. The API **SHOULD** use camelCase for API keys.

> Additional formats, such as PDF and CSV, are allowed in addition to JSON as need requires.

#### Why We Recommend JSON over XML

For decades, DoD Information Systems (IS) have used XML as a preferred data exchange format.  So, why use JSON?  RESTful APIs are designed to be performant and easy to use.  Below are a few comparisons between XML and JSON and why we consider JSON to be best format for REST APIs.

* XML is a document markup language, JSON is not.  JSON is more data-oriented and allows for easy mapping to object-oriented systems.
* Many modern programming languages have JSON support built-in.  There is no need to use or install additional libraries such as those needed for XML parsing.
* Based on syntax, JSON typically produces smaller payloads making more efficient use of network bandwidth.
* Most [client applications](http://githut.info/) are now developed using JavaScript, so it makes sense for APIs to use a native format.
* XML has been around for a while and has many standards around data validations and data compliance.  Though less mature, JSON also has several standards available (e.g. [JSON Schema](http://json-schema.org/), HAL, LD) to handle these situations.
* JSON document stores have gained significant popularity in data stores that can easily store, query, and aggregate using JSON only. This evolution is happening in NoSQL data stores ([elasticsearch](https://www.elastic.co/products/elasticsearch), [mongoDB](https://www.mongodb.com/), [solr](http://lucene.apache.org/solr/), [CouchDB](http://couchdb.apache.org/), etc ) as well as SQL databases like [PostgreSQL](https://www.postgresql.org/docs/current/static/datatype-json.html).

RESTful APIs have historically supported both XML and JSON formats.  Over the past few years, there has been a shift to supporting JSON only.  In addition to the list above, supporting a single format increases maintainability and decreases development time.  For those reasons, we choose to support JSON only.

<hr/>

### <a href="#utf8" id="utf8" class="headerlink"></a> Use UTF-8

[Use UTF-8](http://utf8everywhere.org/).  When sharing data it is important for all systems involved to speak the same language to ensure data is properly and consistently interpreted.  The UTF-8 character encoding is widely used and has become the preferred choice for REST APIs.

An API **SHOULD** tell clients to expect UTF-8 by including a charset notation in the `Content-Type` header for responses.

```http
Content-Type: application/json; charset=utf-8
```

<hr/>

### <a href="#content-negotiation" id="content-negotiation" class="headerlink"></a> Content Negotiation

* ALL content negotiation **SHOULD** be handled using the `Content-Type` and `Accept` headers.

* The `Content-Type` header **SHOULD** be specified in ALL requests/responses:

  ```http
  Content-Type: application/json; charset=utf-8
  ```

* The API **MUST** respond with a `415 Unsupported Media Type` status code if a client specifies a `Content-Type` other than `application/json`.

* Clients **SHOULD** specify a response media type of JSON with the header:

  ```http
  Accept: application/json
  ```

  > If the `Accept` header is not sent, JSON will be sent back by default.

* The API **MUST** respond with a `406 Not Acceptable` status code if a client requests an unsupported response format.

<hr/>

### <a href="#versioning" id="versioning" class="headerlink"></a> Versioning

* Never release an API without a version number.
* Version names **MUST** be integers, not decimal numbers, preceded by the letter `v`: `v1`, `v2`, `v3`...
* Include the version number as part of the request path: `https://[BASE_URL]/[VERSION]/[RESOURCE]`
* The API **SHOULD** respond with a `406 Not Acceptable` status code if a request specifies an unsupported version.
* Increment the version number any time breaking changes are introduced.  Breaking changes are any changes that would cause an existing client to stop working as expected.
* Adding new resources or adding attributes to an existing resource does not require a new version as long as no breaking changes were introduced.
* Maintain APIs at least one version back.

<hr/>

### <a href="#url-naming" id="url-naming" class="headerlink"></a> URL Naming

* A URL identifies a resource.
* URLs should include nouns, not verbs.
* Use plural nouns only (no singular nouns).
* Use HTTP verbs (GET, POST, PATCH, DELETE) to operate on the collections and elements.
* You **SHOULD NOT** need to go deeper than resource/identifier/resource.

Examples:

```
https://example.com/api/v1/albums
https://example.com/api/v1/albums/294
https://example.com/api/v1/albums/294/songs
```

<hr/>

### <a href="#http-verbs" id="http-verbs" class="headerlink"></a> HTTP Verbs

HTTP verbs (GET, POST, PUT, PATCH, DELETE) are used to perform actions on a resource, a collection of resources, or relationships between resources.  For REST APIs, these verbs are mapped to traditional CRUD (create, read, update, and delete) operations.

HTTP Verb | CRUD | Description
----------|------|------------
POST | Create | Creates a new resource
GET | Read | Returns a resource or collection of resources
PATCH | Update | Updates one or more attributes of a resource
DELETE | Delete | Deletes a resource

> PUT **MAY** be used to completely replace all attributes of a resource.

<hr/>

### <a href="#cors" id="cors" class="headerlink"></a> Cross-Origin Resource Sharing (CORS)

For clients to be able to use an API from inside web browsers, the API must [enable CORS](http://enable-cors.org/).

For the simplest and most common use case, where the entire API should be accessible from inside the browser, enabling CORS is as simple as including this HTTP header in all responses:

```http
Access-Control-Allow-Origin: *
```

For more advanced configuration, see the [W3C spec](http://www.w3.org/TR/cors/) or [Mozilla's guide](https://developer.mozilla.org/en-US/docs/Web/HTTP/Access_control_CORS).

<hr/>

### <a href="#authentication" id="authentication" class="headerlink"></a> Authentication/Authorization

The API **SHOULD** use OAuth 2.0 [RFC 6749](https://tools.ietf.org/html/rfc6749) when authenticating clients.

OAuth 2.0 has 4 distinct authentication flows to handle various client types (mobile, browser, web, etc.).  OAuth 2.0 also has the ability to handle authorization based on scopes.  The API **MAY** choose to use OAuth scopes for authorization.

<hr/>

### <a href="#documentation" id="documentation" class="headerlink"></a> Documentation

The API **SHOULD** be documented using the [OpenAPI Specification (fka The Swagger Specification)](https://github.com/OAI/OpenAPI-Specification).

The API **SHOULD** place this documentation at the root path for each version.  For example, documentation for version 1 of an API should be located at: `https://[BASE_URL]/v1`

<hr/>

## <a href="#schema-guidelines" id="schema-guidelines" class="headerlink"></a> Schema Guidelines

RESTful APIs involve the sending (request) and receiving (response) of [JSON](https://tools.ietf.org/html/rfc7159) documents. This section provides guidance on the structure of both request and response documents.

<hr/>

### <a href="#top-level" id="top-level" class="headerlink"></a> Top Level

A JSON object **MUST** be at the root of every request/response document.  This object defines a document's "top level".

> Returning an array at the root level of a response is vulnerable to a CSRF attack whereby an external website can access the response data.

A document **MUST** contain at least one of the following top-level members:

* `meta`: a [meta object][meta objects] containing information about the data being returned.
* `data`: the resource(s) being sent/returned.
* `error`: an [error object][error objects] containing information about any or all errors that occurred during the request.

The members `data` and `error` **MUST NOT** coexist in the same document.

The `data` member **MUST** contain either:

* a single [resource object][resource objects] for requests targeting single resources
* an array of [resource objects] or an empty array for requests targeting resource collections

<hr/>

### <a href="#resource-objects" id="resource-objects" class="headerlink"></a> Resource Objects

A resource object **MUST** contain at least the following top-level members:

* `id`: The unique identifier for the resource [string or integer].
* `href`: The unique url for the resource [string].

In addition, a resource object **MAY** contain any of these top-level members:

* `createdAt`: The timestamp the resource was created [string (ISO 8601)].
* `updatedAt`: The timestamp the resource was last updated [string (ISO 8601)].

<hr/>

### <a href="#meta-objects" id="meta-objects" class="headerlink"></a> Meta Objects

A meta object is used to provide additional information about the data being returned.

A meta object **MUST** contain at least the following top-level members:

* `resourceType`: The type of resource being returned in the `data` member [string].
* `responseTime`: The execution time of the request in seconds [float represented as formatted string].

In addition, a meta object **MAY** contain any of these top-level members:

* `user`: The user ID of the user making the request [string].
* `date`: The date/time the request was received [ISO 8601 Datetime w/ Timezone].
* `pagination`: Pagination information (totalCount, etc.).

<hr/>

### <a href="#summary-representations" id="summary-representations" class="headerlink"></a> Summary Representations

When retrieving a list of [resource objects], the response will include a *subset* of the attributes for that resource.  This is the "summary" representation of the resource.  To obtain all attributes for a resource, retrieve the "detailed" representation.

```http
GET /albums HTTP/1.1
Accept: application/json

{
  "meta": {
    "resourceType": "Album",
    "responseTime": "0.027186"
  },
  "data": [
    {
      "id": "1",
      "href": "/albums/1",
      "title": "For Those About to Rock We Salute You"
    },
    {
      "id": "2",
      "href": "/albums/2",
      "title": "Green River"
    }
  ]
}
```

<hr/>

### <a href="#detailed-representations" id="detailed-representations" class="headerlink"></a> Detailed Representations

When retrieving an individual resource, the response will typically include *all* attributes for that resource.  This is the "detailed" representation of the resource.

```http
GET /albums/1 HTTP/1.1
Accept: application/json

{
  "meta": {
    "resourceType": "Album",
    "responseTime": "0.029371"
  },
  "data": {
    "id": "1",
    "href": "/albums/1",
    "title": "For Those About to Rock We Salute You",
    "coverArt": "https://ia800500.us.archive.org/2/items/mbid-6282e607-18b3-39c2-822b-b8d7bc00c343/mbid-6282e607-18b3-39c2-822b-b8d7bc00c343-1132379641_thumb500.jpg",
    "releasedAt": "1981-11-23",
    "artist": {
      "id": "247",
      "href": "/artists/247",
      "name": "AC/DC"
    },
    "songs": {
      "href": "/albums/1/songs",
      "totalCount": 10
    }
  }
}
```

Sub-resources included in a detailed representation **MUST** use the summary representation

<hr/>

### <a href="#nested-representations" id="nested-representations" class="headerlink"></a> Nested Collection Representations

To-Many relationships, when included in a detailed representation, **MUST** be an object with the following required fields:

* `href`: The url for the nested collection [string].
* `totalCount`: The total number of items in the collection [integer].

For example, when including songs for an album, the songs relationship would be included as follows:

```http
GET /albums/1 HTTP/1.1
Accept: application/json

{
  "meta": {
    "resourceType": "Album",
    "responseTime": "0.027186"
  },
  "data": {
    "id": "1",
    "href": "/albums/1",
    "title": "For Those About to Rock We Salute You",
    ...
    "songs": {
      "href": "/albums/1/songs",
      "totalCount": 10
    }
  }
}
```

<hr/>

## <a href="#retrieving-data" id="retrieving-data" class="headerlink"></a> Retrieving Data

Data can be retrieved by sending a `GET` request to an endpoint.

Responses can be further refined with the optional features described below.

<hr/>

### <a href="#retrieving-resources" id="retrieving-resources" class="headerlink"></a> Retrieving Resources

There are 2 main patterns for retrieve resources:

* Retrieve a collection of resources
* Retrieve a single resource

The following request retrieves a collection of albums:

```http
GET /albums HTTP/1.1
Accept: application/json
```

The following request retrieves a single album:

```http
GET /albums/1 HTTP/1.1
Accept: application/json
```

#### Responses

##### 200 OK

The API **MUST** respond to a successful request to retrieve an individual resource or resource collection with a `200 OK` response.

The API **MUST** respond to a successful request to retrieve a resource collection with an array of [resource objects] or an empty array (`[]`).

A `GET` request to a collection of albums might return:

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "meta": {
    "resourceType": "Album",
    "responseTime": "0.027186"
  },
  "data": [
    {
      "id": "1",
      "href": "/albums/1",
      "title": "For Those About to Rock We Salute You"
    },
    {
      "id": "2",
      "href": "/albums/2",
      "title": "Green River"
    }
  ]
}
```

A similar response representing an empty collection would look like:

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "meta": {
    "resourceType": "Album",
    "responseTime": "0.019229"
  },
  "data": []
}
```

A `GET` request to an individual album might return:

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "meta": {
    "resourceType": "Album",
    "responseTime": "0.027186"
  },
  "data": {
    "id": "1",
    "href": "/albums/1",
    "title": "For Those About to Rock We Salute You",
    "coverArt": "https://ia800500.us.archive.org/2/items/mbid-6282e607-18b3-39c2-822b-b8d7bc00c343/mbid-6282e607-18b3-39c2-822b-b8d7bc00c343-1132379641_thumb500.jpg",
    "releasedAt": "1981-11-23",
    "artist": {
      "id": "247",
      "href": "/artists/247",
      "name": "AC/DC"
    },
    "songs": {
      "href": "/albums/1/songs",
      "totalCount": 10
    }
  }
}
```

##### 404 Not Found

The API **MUST** respond with `404 Not Found` when processing a request to retrieve a single resource that does not exist.

##### Other Responses

The API **MAY** respond with other HTTP status codes.

The API **MAY** include error details with error responses.

<hr/>

### <a href="#partial-resources" id="partial-resources" class="headerlink"></a> Partial Resources

A client **MAY** request that an endpoint return only specific fields in the response by including a `fields` query parameter.

The value of the `fields` parameter **MUST** be a comma-separated list that refers to the name(s) of the fields to be returned.

To return a subset of fields for nested resources, use dot notation for the field names.  For example, to return only the name of an artist, specify the field name as `artist.name`.

If a client requests a specific set of fields using the `fields` query parameter, an endpoint **MUST** include only the mandatory and specified fields.  Additional fields **MUST NOT** be included in the response.

A client **MUST** only specify fields available in the detailed representation for a resource.

The API **MUST** respond with `400 Bad Request` when provided invalid or unavailable field names.

```http
GET /albums?fields=title,coverArt HTTP/1.1
Accept: application/json
```

<hr/>

### <a href="#sorting" id="sorting" class="headerlink"></a> Sorting

The API **MAY** choose to support requests to sort resource collections based on one or more "sort fields".

An endpoint **MAY** support requests to sort the resources with a `sort` query parameter. The value for `sort` **MUST** represent sort fields.

```http
GET /albums?sort=title HTTP/1.1
Accept: application/json
```

An endpoint **MAY** support multiple sort fields by allowing comma-separated sort fields.  Sort fields **SHOULD** be applied in the order specified.

```http
GET /albums?sort=title,coverArt HTTP/1.1
Accept: application/json
```

The sort order for each sort field **MUST** be ascending unless it is prefixed with a minus, in which case it **MUST** be descending.

```http
GET /albums?sort=title,-coverArt HTTP/1.1
Accept: application/json
```

To specify fields for nested resources, use dot notation for the field names.  For example, to sort by the name of an artist, specify the sort field name as `artist.name`.

A client **MUST** only specify sort fields available in the detailed representation for a resource.

If the API does not support sorting as specified in the query parameter `sort`, it **MUST** return `400 Bad Request`.

<hr/>

### <a href="#pagination" id="pagination" class="headerlink"></a> Pagination

The API **MAY** choose to limit the number of resources returned in a response.

The API **MUST** provide links to traverse a paginated data set (“pagination links”).  These links **MUST** be provided using the `Link` header as defined in [RFC5988](https://tools.ietf.org/html/rfc5988).

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

The links **MUST** include all query parameters provided in the request.

The `limit` and `offset` query parameters are reserved for pagination and **SHOULD** be used by the API and clients for pagination operations.

* `limit`: The number of resource objects to return in the response
* `offset`: The location in the overall resultset to begin retrieving resource objects

An example client request using `limit` and `offset`

```http
GET /albums?limit=20&offset=40 HTTP/1.1
Accept: application/json
```

To maintain desired performance and to minimize the possibility of a denial of service attack when requesting large collections, the API **MUST** define `default` and `maximum` values for `limit`.  When `limit` is not specified by the client, the API should return the number of resource objects specified by the `default` value.  If the client specifies the `limit` and it is greater than the `maximum`, the API should only return the number of resource objects specified by the `maximum` value.

The API **MUST** return a `400 Bad Request` if the value specified by the `offset` query parameter exceeds the total resource count of the resource being retrieved.

When returning a paginated response, the API **SHOULD** include a `pagination` object at the root level of the `meta` object:

A pagination object **MUST** contain at least the following top-level members:

* `limit`: The current limit specified by client (or default if not specified)
* `offset`: The current offset specified by client (or default if not specified)
* `count`: The number of results returned in the response
* `totalCount`: The total number of results available

<hr/>

### <a href="#filtering" id="filtering" class="headerlink"></a> Filtering

The API **MAY** choose to support requests to filter resource collections based on one or more criterion.

An endpoint **MAY** support requests to filter the resources with a `filters` query parameter.

#### Single Conditions

Single filters are implemented by providing the name of the field on which to filter followed by the operator on which to test and lastly the value or values on which to test against. The list of valid operators are as follows.

Operator | Description | URL Encoded Operator | Examples
-------- | ----------- | -------------------- | --------
== | equal | %3D%3D | The year published is '2016'<br /> `?filters=yearPublished%3D%3D2016`
!= | not equal | !%3D | The year published is not '2016'<br /> `?filters=yearPublished!%3D2016`
=@ | contains substring | %3D@ | The song title contains the substring 'happy'<br /> `?filters=title%3D@happy`
!@ | does not contain substring | !@ | The song title does not contain the substring 'happy'<br /> `?filters=title!@happy`
> | greater than | %3E | The year published is greater than 2000<br /> `?filters=yearPublished%3E2000`
< | less than | %3C | The year published is less than 2016<br /> `?filters=yearPublished%3C2016`
>= | greater than or equal | %3E%3D | The year published is greater than or equal to 2000<br /> `?filters=yearPublished%3E%3D2000`
<= | less than or equal | %3C%3D | The year published is less than or equal to 2016<br /> `?filters=yearPublished%3C%3D2016`
>=< | inclusively between | %3E%3D%3C | The year published is at least 2000 and at most 2016<br /> `?filters=yearPublished%3E%3D%3C2000;2016`
>< | exclusively between | %3E%3C | The year published is greater than 2000 and less than 2016<br /> `?filters=yearPublished%3E%3C2000;2016`

#### Multiple Conditions

Multiple conditions will be accommodated by separating single conditions with a comma (',').

```
[RESOURCE_URL]?filters=attribute%3D%3Dvalue,attribute2%3E%3CminValue;maxValue
```

To specify fields for nested resources, use dot notation for the field names.  For example, to filter using the name of an artist, specify the filter field name as `artist.name`.

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

<hr/>

## <a href="#creating-resources" id="creating-resources" class="headerlink"></a> Creating Resources

A resource can be created by sending a `POST` request to the collections endpoint for that resource type.  The request **MUST** include a single [resource object][resource objects].

A new album might be created with the following request:

```http
POST /albums HTTP/1.1
Content-Type: application/json
Accept: application/json

{
  "data": {
    "title": "Journeyman",
    "coverArt": "https://ia902304.us.archive.org/2/items/mbid-55767db4-d426-3988-bf4c-5121964cac1d/mbid-55767db4-d426-3988-bf4c-5121964cac1d-8414673474_thumb500.jpg",
    "releasedAt": "1989-11-07",
    "artist": {
      "id": "175"
    }
  }
}
```

#### Responses

##### 201 Created

If the requested resource has been created successfully, the API **MUST** return a `201 Created` status code.

The response **SHOULD** include a `Location` header identifying the location of the newly created resource.

The response **MUST** also include a document that contains the newly created resource.

```http
HTTP/1.1 201 CREATED
Location: https://api.example.com/v1/albums/3
Content-Type: application/json

{
  "meta": {
    "resourceType": "Album",
    "responseTime": "0.034936"
  },
  "data": {
    "id": "3",
    "href": "/albums/3",
    "title": "Journeyman",
    "coverArt": "https://ia902304.us.archive.org/2/items/mbid-55767db4-d426-3988-bf4c-5121964cac1d/mbid-55767db4-d426-3988-bf4c-5121964cac1d-8414673474_thumb500.jpg",
    "releasedAt": "1989-11-07",
    "artist": {
      "id": "175",
      "href": "/authors/175",
      "name": "Eric Clapton"
    },
    "songs": {
      "href": "/albums/3/songs",
      "totalCount": 0
    }
  }
}
```

##### 400 Bad Request

The API **MAY** return `400 Bad Request` if unable to create the resource due to missing or invalid data.

##### 403 Forbidden

The API **MAY** return `403 Forbidden` in response to an unsupported request to create a resource.

##### Other Responses

The API **MAY** respond with other HTTP status codes.

<hr/>

## <a href="#updating-resources" id="updating-resources" class="headerlink"></a> Updating Resources

A resource can be updated by sending a `PATCH` request to the URL that represents the resource.

The `PATCH` request **MUST** include a single [resource object][resource objects].

```http
PATCH /albums/1 HTTP/1.1
Content-Type: application/json
Accept: application/json

{
  "data": {
    "title": "My Updated Title"
  }
}
```

#### Updating a Resource's Attributes

Any or all of a resource's attributes **MAY** be included in the resource object included in a `PATCH` request.

If a request does not include all of the attributes for a resource, the API **MUST** interpret the missing attributes as if they were included with their current values.  The API **MUST NOT** interpret missing attributes as `null` values.

#### Updating a Resource's Relationships

Any or all of a resource's relationships **MAY** be included in the resource object included in a `PATCH` request.

If a request does not include all of the relationships for a resource, the API **MUST** interpret the missing relationships as if they were included with their current values.  The API **MUST NOT** interpret missing relationships as `null` or empty values.

If a relationship is included in the resource object included in a `PATCH` request, the relationship's value will be replaced with the value specified in the request.  A complete replacement will be performed on to-many relationships.

The API **MAY** reject an attempt to do a full replacement of a to-many relationship.  If so, the API **MUST** reject the entire update, and return a `403 Forbidden` response.

#### Responses

##### 200 OK

The API **MUST** return a `200 OK` status code if an update is successful.  The response document **MUST** include a representation of the updated resource as if a `GET` request was made.

##### 400 Bad Request

The API **MAY** return `400 Bad Request` if unable to update the resource due to missing or invalid data.

##### 403 Forbidden

The API **MAY** return `403 Forbidden` in response to an unsupported request to update a resource.

##### 404 Not Found

The API **MUST** return `404 Not Found` when processing a request to modify a resource (or related resource) that does not exist.

##### Other Responses

The API **MAY** respond with other HTTP status codes.

<hr/>

### <a href="#updating-relationships" id="updating-relationships" class="headerlink"></a> Updating Relationships

Although relationships can be modified along with resources, the API **MAY** provide links to modify them independently.

#### Updating To-One Relationships

If the API allows independent modification of relationships...

The API **MUST** respond to `PATCH` requests for the to-one relationship URL.

The `PATCH` request **MUST** include a top-level member named `data` containing one of the following:

* a resource identifier object corresponding to the new related resource.
* `null`, to remove the relationship.

A request to update the artist of an album with `id` of `1`:

```http
PATCH /albums/1/artist HTTP/1.1
Content-Type: application/json
Accept: application/json

{
  "data": {
    "id": "339"
  }
}
```

A request to clear the artist of an album with `id` of `1`:

```http
PATCH /albums/1/artist HTTP/1.1
Content-Type: application/json
Accept: application/json

{
  "data": null
}
```

If the relationship is updated successfully then the API **MUST** return a successful response.

#### Updating To-Many Relationships

If the API allows independent modifications of relationships...

The API **MUST** respond to `PATCH`, `POST`, and `DELETE` requests for the to-many relationship URL.

For all request types, the body **MUST** contain a `data` member whose value is an empty array or an array of resource identifier objects.

If a client makes a `PATCH` request, the API **MUST** either completely replace every member of the relationship, return an appropriate error response if some resources can not be found or accessed, or return a `403 Forbidden` response if complete replacement is not allowed by the API.

The following request replaces every song for an album with `id` of `1`:

```http
PATCH /albums/1/songs HTTP/1.1
Content-Type: application/json
Accept: application/json

{
  "data": [
    { "id": "13" },
    { "id": "43" }
  ]
}
```

The following request clears every song for an album with `id` of `1`:

```http
PATCH /albums/1/songs HTTP/1.1
Content-Type: application/json
Accept: application/json

{
  "data": []
}
```

If a client makes a `POST` request, the API **MUST** add the specified members to the relationship unless they are already present.  If a given member is already in the relationship, the API **MUST NOT** add it again.

If all of the specified resources can be added to, or are already present in, the relationship then the API **MUST** return a successful response.

The following request adds the song with ID `345` to the list of songs for the album with `id` of `1`:

```http
POST /albums/1/songs HTTP/1.1
Content-Type: application/json
Accept: application/json

{
  "data": [
    { "id": "345" }
  ]
}
```

If the client makes a `DELETE` request, the API **MUST** delete the specified members from the relationship or return a `403 Forbidden` response.  If all of the specified members resources are able to be removed from, or are already missing from, the relationship then the API **MUST** return a successful response.

The following request removes songs with IDs `17` and `29` from the album with `id` of `1`:

```http
DELETE /albums/1/songs HTTP/1.1
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

The API **MUST** return a `200 OK` status code if an update is successful.  The response document **MUST** include a representation of the updated relationship(s).

##### 403 Forbidden

The API **MUST** return `403 Forbidden` in responses to an unsupported request to update a relationship.

##### Other Responses

The API **MAY** respond with other HTTP status codes.

<hr/>

## <a href="#deleting-resources" id="deleting-resources" class="headerlink"></a> Deleting Resources

An individual resource can be deleted by making a `DELETE` request to the resource's URL:

```http
DELETE /albums/1 HTTP/1.1
Accept: application/json
```

#### Responses

##### 204 No Content

The API **MUST** return a `204 No Content` status code if a deletion request is successful and no content is returned.

##### Other Responses

The API **MAY** respond with other HTTP status codes.

<hr/>

## <a href="#query-parameters" id="query-parameters" class="headerlink"></a> Query Parameters

If the API encounters a query parameter that it does not know how to process, it **MUST** return `400 Bad Request`

<hr/>

## <a href="#errors" id="errors" class="headerlink"></a> Errors

### <a href="#processing-errors" id="processing-errors" class="headerlink"></a> Processing Errors

The API **MAY** choose to stop processing as soon as a problem is encountered, or it **MAY** continue processing and encounter multiple problems.

When the API encounters multiple problems for a single request, the most generally applicable HTTP error code **SHOULD** be used in the response. The following is a list of common error codes:

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
