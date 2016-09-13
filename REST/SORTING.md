# REST API Sorting

## Summary
This document covers the convention for sorting a result set for a given API resource. Similar to filtering, a generic parameter sort can be used to describe sorting rules. Sorting is the method by determining the order in which items in a payload are returned from a service.

## Sorting
When returning a collection, certain resources may be sortable. The recommended approach is to utilize a sort query-string parameter that contains a list of comma separated fields, each with a possible unary negative to imply descending sort order. For each attribute name, the default sort order will be in ascending order. For each attribute prefixed with a dash (“-”), the results shall be sorted in descending order.
List resources can be sorted by a single attribute or multiple attributes. To sort by multiple attributes, separate the sort terms with commas (","). The results will initially be sorted by the first attribute in the direction specified. Then, the results within each set will be sorted by the second attribute.Please refer to the endpoint documentation for the specified valid sort values. Sorting should be offered on a case-by-case basis for only resources that need it. Small collections of resources can be ordered on the client, if needed.

### Parameters

Name | Type | Description
---- | ---- | -----------
sort | string | Specifies that the query results should be sorted by the attribute value.

> The sort parameter will not be supported for POST, PUT, DELETE operations. Only GET list resources will support sortparameters.

### Example

```
https://[BASE_URL]/api/v1/[RESOURCE]?sort=createdAt,-name
```

### Default Assumptions

* Strings are sorted in ascending alphabetical order in en-US locale.
* Numbers are sorted in ascending numeric order.
* Dates are sorted in ascending order by date.
