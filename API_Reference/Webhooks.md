# Kragle.io Documentation

### [Table of Contents](../README.md)

##### [Previous Topic: Stacks](./Stacks.md)

##### [Next Topic: Jobs](./Jobs.md)

## API Reference - Webhooks

### GET /blockstacks/hook/\<stack id>
### POST /blockstacks/hook/\<stack id>

_(Authentication requirements for these endpoints are controlled via the 'webhook' property of a Stack. Individual Stacks may or may not require authentication of some form when accessed as Webhooks.)_

These endpoints are used to trigger a Stack execution in the style of 'webhooks' common on many sites/services.

Accessing a url (either via GET or POST, depending on the setup of the Stack) will cause it to execute.

Regardless of the HTTP verb used to access the hook, the Stack being executed will receive the following structure as it's input:
```
{
    "GET": {
        "query_string_param_1": "value 1",
        "query_string_param_present_more_than_once": ["first value", "second value"]
    },
    "POST": {},
    "JSON": <a json object, if the request contained one as it's body, or {}>,
    "HEADERS": {
        "Content-type": "application/json; charset=utf-8",
        "another-header": "another header value"
    }
}
```

**NB:** Both the `POST` and `GET` elements of the input contain values which are either single values if the parameter in question was present in the request/query string/etc only once, or an array of values if it was duplicated/multiple values were sent for the parameter/etc.

The response from a webhook execution will be one of the following. For success, this structure will be returned:
```
{
    "status": 0,
    "result": <the structure output by the Stack>
}
```

For a failure, ie: an error of some kind while processing the stack, this structure will be returned:
```
{
    "status": 1,
    "err": <string representing the error which ocurred>
}
```

**NB:** If the request itself is successful, the HTTP response code will be the usual 200 OK - any errors generated during the execution of the Stack will be returned in the response body of the 200 ok as per the example above, **not** via HTTP error codes.

**Required parameters**:
  - None

**Optional parameters**:
  - None

### GET /blockstacks/exec/<stack id>

_(This endpoint requires authentication.)_

This endpoint allows you to manually trigger (ie: run/execute) a non-webhook Stack (ie: one whose 'webhook' property is null). It is useful for testing, debugging, or simply creating Stacks intended to be executed on demand as opposed to periodically via a Schedule, etc.

The response to a request of this type is a JSON object. For success, this structure is returned:
```
{
    "success": [<a list of block instance ids which were executed>]
}
```

If an error occurs during execution of the stack, this structure will be returned instead:
```
{
    "error": <a string describing the error which occurred>
}
```

In both cases, the HTTP response code, as with regular webhooks, will be 200, indicating that the request itself proceeded without error and there was no issue with the request data/etc (rather, the error in question was internal to the stack execution itself).

**Required parameters**:
  - None

**Optional parameters**:
  - None

### GET /blockstacks/hook/token_refresh/<stack id>

_(This endpoint requires authentication.)_

This endpoint will refresh the 'webhook_token' value for the Stack.

The response is a JSON object in the following format:
```
{
    "token": <new webhook token>
}
```

**Required parameters**:
  - None

**Optional parameters**:
  - None

##### [Next Topic: Jobs](./Jobs.md)

##### [Previous Topic: Stacks](./Stacks.md)

### [Back to Table of Contents](../README.md)

