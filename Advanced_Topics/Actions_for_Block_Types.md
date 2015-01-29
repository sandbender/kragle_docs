# Kragle.io Documentation

### [Table of Contents](../README.md)

##### [Previous Topic: Creating your own Block types](./Creating_Block_Types.md)

##### [Next Topic: Types are really just JSON Schema](./Types.md)

## Actions available for Block types

The following list details all the currently available actions for use in a Block type definition, along with their lists of required/optional parameters, etc.

#### uri_get, uri_head, uri_post

These actions are used to GET, HEAD and POST to/from a given url, respectively.

Required Params:
- uri - string - The uri you with to access via the HTTP verb in question. 

Optional Params:
- headers - object - An object representing HTTP headers to send with the request, where keys are header names and values are the header values. Values must be strings. Default: `{}`.
- response_type - string (enum) - One of 'string', 'json' or 'xml'. If 'string', the response body will be returned as a raw string. If 'json', the body will be parsed as json and the resulting object returned. If 'xml', the body will be parsed as xml, **and converted to a representative json structure**, and returned. Default: 'string'.
- body - string - **Ony valid for uri_post**. The body to POST to the url with the request. Default: no body is sent (null string).
- auth_required - boolean - Indicates that the request will require some form of authentication. Default: 'false'.
- auth_type - string (enum) - One of 'strategy' or 'basic'. **No default.**

Dependencies/Etc:
- If 'auth_required' is present, 'auth_type' must also be present.

Return Value: The appropriate representation of the response body, based on the 'response_type' param.

#### uri_get_with_headers, uri_head_with_headers, uri_post_with_headers

These actions mirror the non _with_headers versions exactly, with the exception that they return the response headers of the request along with the body, and so the return value is an object containing both body and headers, instead of the body itself.

Return Value: An object such as the following, with the value of 'body' dependent upon the 'response_type' param...
```
{
  "headers": {
    "header_name_one": "value for header_name_one",
    ...
  },
  "body": <body value>
}
```

#### convert

The 'convert' action is a highly flexible mechanism for converting one arbitrary data structure into another based on an input structure.

The input to the action (ie: output from previous Action/Block) will be used as the 'source' data for the conversion, and the 'conversion' parameter to this action will be the structure to convert based on the input.

(See the [API-specific Topics: Conversion Specifiers](../API-specific_Topics/Conversion_Specifiers.md) section for full details on how this works.)

Required Params:
- conversion - object/array/string/null - The structure to convert.

Optional Params:
- None

Dependencies/Etc:
- None

Return Value: The result of applying the input to the conversion structure, ie: the resulting data structure.

##### [Next Topic: Types are really just JSON Schema](./Types.md)

##### [Previous Topic: Creating your own Block types](./Creating_Block_Types.md)

### [Back to Table of Contents](../README.md)

