# REST API Pagination

## Summary

This document covers the convention for pagination for a given API Product. Pagination is the method by which implementers can divide content into discrete pages of a given data set.

## Limit and Offset

When returning a collection, the API method may return one of two responses:1. The entire collection in one response (depending on the size of the dataset).
2. A part of the collection, requiring the user agent to make repeated requests to retrieve the rest of the collection (larger data set).The use of limit and offset parameters shall be used to allow navigation through larger data sets.

### Parameters

Name | Type | Description | Default Value | Max Value
---- | ---- | ----------- | ------------- | ---------
offset | integer | The offset of the first record returned. | N/A | Total number of records minus 1.
limit | integer | The number of records returned. | 50 unless otherwise noted | No max limit unless otherwise noted

The API implementation may define a default limit parameter if nothing is passed in. The default limit will depend on the size of the payload that is returned.

### Example Request URL

```
https://[BASE_URL]/api/v1/[RESOURCE]?limit=20&offset=50
```

> As new records are added to the data store the total count will increase. This may cause you to skip over or see duplicate values when using limit and offset.

## API Response

Information regarding pagination should be included in the "meta" property at the top-level of the API response.

### Example

```json
{  "meta": {    "pagination": {      "limit": 20,      "offset": 50,      "count": 13,      "totalCount": 63    },
    ...
  }
}
```

## Link Headers

The API method shall include paging details using Link headers and should return a set of ready-made links so the API consumer doesn't have to construct links themselves. The API shall implement previous and next links for simple pagination.

### Example

```
Link: <https://[BASE_URL]/api/v1/[RESOURCE]/?limit=20&offset=80>; rel="next",      <https://[BASE_URL]/api/v1/[RESOURCE]/?limit=20&offset=40>; rel="previous"
```
Please refer to RFC 5988 on how to include pagination details using the 'Link Header'.
