# Kragle.io Documentation

### [Table of Contents](../README.md)

##### [Previous Topic: Non-OAuth access management](./Access_Mgmt.md)

##### [Next Topic: A Full Example/Walkthrough via the API](../Full_Example_API.md)

## Faults and Error Codes

All request-based errors to the Kragle api will generate an HTTP 500 (Internal Server Error) with a JSON body describing the error.

The error JSON bodies will have the following format:
```
{
    "code": <numeric error code > 0>,
    "msg": <human-readable error message>
}
```

The following list outlines each possible error code and associated message, and provides a short description/explanation for cases where the error message itself is not self-explanatory.

### Code: 1
##### You have made a bad request - Please check your query params and try again.

This error is generally returned if you make a request with invalid parameters. An example would be passing a value for the `page` parameter of a paged-results request, which is not an integer.

### Code: 2
##### Your request body contained invalid or inappropriate JSON - please check your request and try again.

This will be returned if the provided request JSON is not valid JSON/cannot be parsed for some reason.

### Code: 3
##### That user does not exist or the specified password is incorrect.

Returned as a result of failed login attempts via the api.

### Code: 4
##### Your json request payload is inappropriate for the requested operation.

This is a common error which will be returned if the JSON payload of your request contains invalid keys for the operation in question, is missing required parameters, has parameters with invalid values, etc.

### Code: 5
##### There is already a Type with that name or you have specified a bad name... please try again.

### Code: 6
##### The specified object does not exist.

Common error returned when an API request for a specific object (ie: via id or name) fails because an object with the specified id (name, etc) does not exist.

### Code: 7
##### Could not retrieve valid JSON from specified remote URL.

Returned when a definition (url) for an is_remote object (Block, Type) does not exist at the given url or the url did not return valid JSON.

### Code: 8
##### The provided JSON structure for a Type was invalid.

The 'definition' provided (either via remote url or directly) for a new/updated Type is invalid.

### Code: 9
##### The provided JSON structure for a Block was invalid.

The 'definition' provided (either via remote url or directly) for a new/updated Block is invalid.

### Code: 10
##### The named Stack already exists.

### Code: 11
##### The Stack contained a Block with invalid "extra" args.

The array representing the Blocks for a new or updated Stack contained an entry with invalid "extra" parameters for the block (ie: in position 2 (3rd element) of one of the Block arrays in the 'blocks' element of the request JSON).

### Code: 12
##### The named Job already exists.

### Code: 13
##### You may only use one "uri" type Action per Block.

### Code: 14
##### Bad input to validation utility.

You are missing either the 'schema' or 'data' elements of the JSON payload for a validation request.

### Code: 15
##### Bad input to conversion utility.

You are missing either the 'conversion' or 'data' elements of the JSON payload for a conversion request.

### Code: 16
##### The provided schema (or schema url) is not a valid JSON Schema.

### Code: 17
##### Your conversion produced an error.

The attempted conversion resulted in an internal error - check your data and/or conversion specifier.

### Code: 18
##### Your validation produced an error.

The provided data resulted in an internal error while attempting to validate against the provided schema - check your data and/or schema.

### Code: 19
##### The uri provided for a JSON Schema was invalid or a json object could not be loaded from the response.

### Code: 20
##### The type you are trying to modify is already approved (public) - you can not modify "is_remote" or "definition"!

### Code: 21
##### The schema uri you passed was invalid or a json object could not be loaded from the result.

### Code: 22
##### You passed a non-existent Block type in your list of Blocks for a Stack.

### Code: 23
##### One of the specified Blocks used in your Stack does not exist, or does not belong to you and is not public.

### Code: 24
##### The specified Stack used in your Job does not exist, or does not belong to you and is not public.

##### [Next Topic: A Full Example/Walkthrough via the API](../Full_Example_API.md)

##### [Previous Topic: Non-OAuth access management](./Access_Mgmt.md)

### [Back to Table of Contents](../README.md)

