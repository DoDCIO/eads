# REST API Content Negotiation

## Summary

## Content Type

The media type of the entity-body being sent to the recipient should be specified using the Content-Type entity-header. This header should be set for both client requests and REST API responses.
```
Content-Type: application/json;
```

### Acceptable Media Types
* JSON: `application/json`, `text/json`
* XML: `application/xml`, `text/xml`

> If the REST API receives a media type not listed in the acceptable media types, it should respond with the status code 406 Not Acceptable.

## Content Negotiation

REST APIs are capable of responding with various media type representations of the requested resource. The client should specify the preferred media type (JSON, XML) in the request using one of the methods below:

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

The Accept header can accept multiple media types along with quality factors to specify media type preference. See Section 14.1 of RFC2616 for more information.

### Precedence

When determining the appropriate media type for a response, the REST API will use the following order: 

1. Query Parameter 2. URL Extension 3. Accept Header 
The first method found will be used. For example, if a query parameter and the Accept header are both specified, the REST API will use the query parameter.
>If no method is used to specify the preferred media type, json will be sent back by default.