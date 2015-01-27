# Kragle.io Documentation

### [Table of Contents](./README.md)

##### [Previous Topic: Conversion Functions](./API-specific_Topics/Conversion_Functions.md)

##### [Next Topic: Authentication](./API_Reference/Authentication.md)

## API Reference

All access to the api is via HTTP REST-style endpoints.

Unless otherwise noted, all request bodies (POST payloads) must be valid JSON. In the documentation, required/optional parameters for an endpoint will imply that these parameters exist as top-level elements of the main JSON body-object. Any nested values will be described/explained in the relevant endpoint documentation, but generally speaking an API request will always be a JSON value which is an object with one 'level' of properties (although the property values themselves may/will often be more complex structures).

Unless otherwise noted, all response bodies will contain valid JSON.

All access to the Kragle API must use secure (HTTPS) connections.

### Query-string parameters (vs. request bodies, etc)

For POST and PUT endpoints, all parameters are part of the top-level JSON object in the request body.

For GET and DELETE endpoints, all parameters are passed as query-string parameters in the url.

In some cases, there may be a possible mix of query-string and JSON-body parameter usage - in such cases, the documentation will clearly explain the proper place for the parameter(s) in question.

Complex data structures (objects and arrays) are generally never passed in query strings.

Query-string parameters which are booleans should be passed as 0 (representing false) or 1 (representing true).

### Errors

All API-generated errors will be returned as HTTP 500 responses with JSON bodies.

Error bodies will follow this format:
```
{
    "code": <integer>,
    "msg": <human readable error message>
}
```

See [Faults and Error Codes](./API_Reference/Faults.md) for a list of potential errors.

Generally speaking, each endpoint will have a finite set of potential errors it can return, however by convention **any** endpoint may return **any** error code. As such, your code which speaks to Kragle should simply watch for 500 HTTP response codes, perform any code-specific handling if necessary, and then fallback to some form of default error handling if the error code is unexpected.

Because of this, potential error codes are **not** listed on a per-endpoint basis in the subsequent API documentation, but rather all together in the final "[Faults and Error Codes](./API_Reference/Faults.md)" section.

### Authentication

Kragle uses signed cookies for session identification; as such, when making requests which require authentication you must include the appropriate Cookie header(s) containing the cookies you receive when authenticating (ie: against the POST /api/v1/login endpoint, etc).

#### Inline Authentication

Kragle also supports inline authentication. This allows you to authenticate 'on the fly' when making an API request. In order to make use of this, your json request body will instead be located inside the 'payload' element of a new JSON body, and your authentication credentials (either `username` or `email`, AND `password`) are passed in an 'auth' element of the new body. Here is an example JSON structure for a request which uses inline authentication:
```
{
    "auth": {"username": "SandbenderCa", "password": <password>},
    "payload": <the JSON structure you are sending as the request payload>
}
```

### API endpoints

All api requests are made via a sub-path of the following, against https://kragle.io (or the appropriate sandbox hostname, etc):

`/api/v1`

For example, this would be the complete url to make a request to the api Login method:

`https://kragle.io/api/v1/login`

In the subsequent [API Reference](../../tree/master/API_Reference) documentation, all urls are specified as this sub-path. Ie: in the documentation, a url specified simply as `/login` will refer to the above complete/correct path.

#### API Versions

All api endpoints will be documented with the versions of the api to which they apply. Future api version numbers will be used in place of '1' above, ie: for a theoretical version 2.1 api endpoint, you could use this url to login instead (assuming /login is valid in the theoretical 2.1 version of the api):

`https://kragle.io/api/v2.1/login`

##### [Next Topic: Authentication](./API_Reference/Authentication.md)

##### [Previous Topic: Conversion Functions](./API-specific_Topics/Conversion_Functions.md)

### [Back to Table of Contents](./README.md)
