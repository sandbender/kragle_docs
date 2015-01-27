# Kragle.io Documentation

### [Table of Contents](../README.md)

##### [Previous Topic: Non-OAuth access management](./Access_Mgmt.md)

## Faults and Error Codes

All request-based errors to the Kragle api will generate an HTTP 500 (Internal Server Error) with a JSON body describing the error.

The error JSON bodies will have the following format:
```
{
    "code": <numeric error code > 0>,
    "msg": <human-readable error message>
}
```

The following list outlines each possible error code and associated message, along with a short description.

### Code: 1
##### Message: You have made a bad request - Please check your query params and try again.

This error is generally returned if you make a request with invalid parameters, or parameter combinations which are dis-allowed, etc.

### Code: 2
##### Message: Your request body contained invalid or inappropriate JSON - please check your request and try again.

This will be returned if the provided request JSON is not valid JSON/cannot be parsed for some reason, or if the contents are somehow invalid. (Generally speaking, invalid contents will usually result in a more specific error).

### Code: 3
##### Message: That user does not exist or the specified password is incorrect.

Returned as a result of failed login attempts via the api.

##### [Previous Topic: Non-OAuth access management](./Access_Mgmt.md)

### [Back to Table of Contents](../README.md)

