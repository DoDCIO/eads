# REST API Base Result Model

## Summary

To ease development of clients, new APIs must format their output according to a common data model called the REST API Base Result Model. This model defines common ways to represent response metadata, such as the timestamp of the request and type of resource returned. Subsets of the API suite may be required to implement additional data model components, which are defined in "sub-models" of the REST API Base Result Model. Sub-models include all of the requirements in the base result model, along with additional domain-specific requirements defined separately from this document.

The base result model can be serialized in one syntax which is JSON. Details of the result model serialization format is defined below.

> The REST API Base Result Model only defines the formatting of successful API calls (API calls whose response has an HTTP status code of
2xx).

## Base Result Model (JSON)

Responses to all successful API requests should be formatted using the Base Result Model's JSON syntax, which is a JSON object containing two child elements: meta and data.

### Meta Property

The 'meta' property contains the metadata relevant to the response. The following table displays properties that may be included under meta:

Property | Required | Type | Description
---- | -------- | ---- | -----------
resourceType | YES | string | The type of resource being returned in the 'data' property of the response
user | NO | string | The user ID used to make the request
date | NO | string (ISO 8601 Datetime w/ Timezone) | The date/time the request was received
responseTime | NO | integer | The execution time of the request in seconds

> Within the meta object, sub-models of the Base Result Model may require additional fields and may define additional meta data fields, but no metadata fields from the Base Result Model shall be altered or removed.

### Data Property

The 'data' property contains the payload of the response. The following table displays properties that may be included under data:

Property | Required | Type | Description
---- | -------- | ---- | -----------
createdAt | YES | string (ISO 8601 Datetime w/ Timezone) | The date/time the resource was created
updatedAt | YES | string (ISO 8601 Datetime w/ Timezone) | The date/time the resource was last updated

### Example

```json
{
  "meta": {
    "resourceType": "User",
    "user": "B725FB21",
    "date": "2014-01-25T03:55:30Z",
    "responseTime": 0.003
  },
  "data": {
    "id": "A93CE298",
    "firstName": "John",
    "lastName": "Smith",
    "createdAt": "2014-01-25T03:55:30Z",
    "updatedAt": "2014-01-25T03:55:30Z"
  }
}
```

## Successful Response Behavior

HTTP Method | Request Body | Status Code | Response Body
----------- | ------------ | ----------- | -------------
GET | None | 200 | Resource(s) returned in 'data' property
POST | Resource | 201 | Created resource returned in 'data' property
PUT | Resource properties | 200 | Updated resource returned in 'data' property
DELETE | None | 204 | None
