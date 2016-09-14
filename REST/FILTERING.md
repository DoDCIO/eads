# REST API Filtering

## Summary

This document covers the convention for filtering a result set for a given API resource. Filtering is the method that restricts/reduces the results returned from a collection into specific subsets of data. This allows network traffic and processing demands to be reduced by limiting the data transmitted from the data store to the user interface to relevant data.

## Filtering

When returning a collection, certain resources may support filtering. The recommended approach is to utilize a query-string parameter. To filter, perform a GET request and put your filters as the "filters" parameter of the query string. List resources can be filtered by a single condition or multiple conditions. Filter conditions against the same field will be logically combined using OR. After filter conditions have been combined within fields, conditions against different fields will be combined using logical AND.

Please refer to the endpoint documentation for the specified valid filter attributes for each resource. Filtering should be offered on a case-by-case basis for only list resources that need it. Small collections of resources can be filtered on the client, if needed.

### Parameters

Name | Type | Description
---- | ---- | -----------
filters | string | Specifies the query results should be filtered by the given conditions.

> The filter parameter will not be supported for POST, PUT, DELETE operations. Only GET list resources will support filtering parameters.

### Single Conditions

Single filters are implemented by providing the name of the field on which to filter followed by the operator on which to test and lastly the value or values on which to test against. The list of valid operators are as follows.

Operator | Description | URL Encoded Operator | Examples
-------- | ----------- | -------------------- | --------
== | equal | %3D%3D | First Name is 'John'<br /> `?filters=firstName%3D%3DJohn`
!= | not equal | !%3D | First Name is not 'John'<br /> `filters=firstName!%3DJohn`
> | greater than | %3E | Cost is greater than 400<br /> `?filters=cost%3E400`
< | less than | %3C | Cost is less than 400<br /> `?filters=cost%3C400`
>= | greater than or equal | %3E%3D | Cost is greater than or equal to 400<br /> `?filters=cost%3E%3D400`
<= | less than or equal | %3C%3D | Cost is less than or equal to 400<br /> `?filters=cost%3C%3D400`
>=< | inclusively between | %3E%3D%3C | Cost is at least 400 and at most 1000<br /> `?filters=cost%3E%3D%3C400;1000`
>< | exclusively between | %3E%3C | Cost is greater than 400 and less than 1000<br /> `?filters=cost%3E%3C400;1000`

### Multiple Conditions

Multiple conditions will be accommodated by separating single conditions with a comma (',').

```
[RESOURCE_URL]?filters=attribute%3D%3Dvalue,attribute2%3E%3CminValue;maxValue
```

### Special Characters

As the comma and semi-colon are assigned special meaning, any instance of the following characters contained within individual values must be escaped.

Character | Text | Escaped As
--------- | ---- | ----------
semicolon | ; | \;
comma | , | \,
backslash | \ | \\\

```
[RESOURCE_URL]?filters=fullName%3D%3DMontoya\,Inigo
```
