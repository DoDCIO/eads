<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css" integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u" crossorigin="anonymous">

# REST API Errors

## Summary

This document covers the convention for returning error responses for an API. Whenever an API request is unsuccessful, an error response should be returned containing meaningful information to resolve the issue. This document will describe the necessary information to include in these error responses.

## Response

Error responses should contain the following parameters:

Parameter  | Description | Header/Body | Required 
------------- | ------------- | ------------- | -------------
developerMessage | Provides developer information about error | Body | YES
userMessage | Informational message for end-users | Body | NO 
errorCode | Application specific error code | Body | YES 
moreInfo | link to find additional information about the error | Body | NO 

### Example:

```json

{  "error": {    "developerMessage" : "A detailed description of the problem with suggestions on how to solve.",
    "userMessage" : "An informational message for end-users.",
    "errorCode" : 9583,
    "moreInfo" : "https://link/to/additional/information"
  }}

```

## HTTP Status Codes

HTTP status codes should be used to indicate successful and unsuccessful requests. The following 3 classes of status codes cover all interactions between the client application and the API.

Status Code Class  | Description 
------------- | ------------- 
<span class="label label-success">2xx</span> | Request was successful
<span class="label label-warning">4xx</span> | The application did something wrong (Client)
<span class="label label-danger">5xx</span> | The API did something wrong (Server)

An appropriate status code should be returned based on the specific type of successful or unsuccessful response. Below is a list of acceptable status codes:

Status Code | Status Message | Description 
----------- | -------------- | -----------
200 | OK | Request was successful. This status code is used for successful GET, PUT, and DELETE requests.
201 | CREATED | Resource was created successfully. This status code is used for successful POST requests.
204 | NO CONTENT | Resource was deleted successfully.  This status code is used for successful DELETE requests.
400 | BAD REQUEST | Request failed. This is mostly due to missing or invalid request parameters.
401 | UNAUTHORIZED | Request failed. This status code is used for missing or invalid credentials (NOT AUTHENTICATED).
403 | FORBIDDEN | Request failed. This status code is used for insufficient privileges (NOT AUTHORIZED).
404 | NOT FOUND | Request failed. This status code is used when the requested resource does not exist.
405 | METHOD NOT ALLOWED | Request failed. This status code is used when the request is known but not supported by the target resource.
500 | INTERNAL SERVER ERROR | Request failed. An error occurred on the server.
