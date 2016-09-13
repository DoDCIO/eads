# REST API Field Limiting

## Summary

This document covers the convention for field limiting a result set for a given API resource. A single field query parameter for each response that implements field limiting can be used to describe limiting response rules. Field limiting is the method by limiting which fields are returned by the API response.

## Field Limiting

When returning a collection, certain field resources may be returned to limit the response. The recommended approach is to utilize a field query-string parameter that contains a list of comma separated fields. All resources can be sorted by a single field or multiple fields. To limit by multiple fields, separate the field terms with commas (','). If you specify the fields parameter ONLY the fields you request are returned along with the ID and item type for the response. By utilizing field parameters, the request would only retrieve enough information to display for the response which can help minimize payload times.
Please refer to the endpoint documentation for the specified valid field parameters. Field limiting should be offered on a case-by-case basis for only resources that need it. Small collections of resources can be ordered on the client, if needed.

### Parameters

Name | Type | Description
---- | ---- | -----------
fields | string | Specifies the query results should be limited to the specified fields

> The fields parameter will not be supported for POST, PUT, DELETE operations. Only GET list and instance resources will support field parameters.

### Example

```
https://[BASE_URL]/api/v1/[RESOURCE]?fields=value,value2
```
