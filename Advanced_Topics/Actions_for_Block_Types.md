# Kragle.io Documentation

### [Table of Contents](../README.md)

##### [Previous Topic: Creating your own Block types](./Creating_Block_Types.md)

##### [Next Topic: Types are really just JSON Schema](./Types.md)

## Actions available for Block types

The following list details all the currently available actions for use in a Block type definition, along with their lists of required/optional parameters, etc.

#### uri_get, uri_head, uri_post

These actions are used to GET, HEAD and POST to/from a given url, respectively.

Required Params:
- `uri` - string - The uri you with to access via the HTTP verb in question. 

Optional Params:
- `headers` - object - An object representing HTTP headers to send with the request, where keys are header names and values are the header values. Values must be strings. Default: `{}`.
- `response_type` - string (enum) - One of 'string', 'json' or 'xml'. If 'string', the response body will be returned as a raw string. If 'json', the body will be parsed as json and the resulting object returned. If 'xml', the body will be parsed as xml, **and converted to a representative json structure**, and returned. Default: 'string'.
- `body` - string - **Ony valid for uri_post**. The body to POST to the url with the request. Default: no body is sent (null string).
- `auth_required` - boolean - Indicates that the request will require some form of authentication. Default: 'false'.
- `auth_type` - string (enum) - One of 'strategy' or 'basic'. **No default.**

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
- `conversion` - object/array/string/null - The structure to convert.

Optional Params:
- None

Dependencies/Etc:
- None

Return Value: The result of applying the input to the conversion structure, ie: the resulting data structure.

#### exclude_in

This action returns an array of elements from an input array, with some element potentially removed based on the value of a sub-element.

It will loop through each element of the input array, and based on `element_path` will examine a value within that element. If the value in question matches a value in the `exclusions` array, the whole element will be omitted from the resulting array.

For example, if this is our input array:

```json
[
    {"message": "hello", "length": 5},
    {"message": "goodbye", "length": 7}
]
```

... we could filter out any element whose 'length' property is 5 with the following parameters to exclude_in:

```json
{
    "element_path": "/length",
    "exclusions": [5]
}
```

Required Params:
- `element_path` - string - The "path" to the sub-element in each input element on which we want to filter.
- `exclusions` - array - An array of `element_path` values which should **not** be included in the output array.

Optional Params:
- None

Return Value: The filtered array of elements.

#### filter

This action will filter an input array of elements directly, according to an exclusion list.

For each element in the input array, if that element's value matches a value in the exclusion list, it will be omitted in the resulting array.

Required Params:
- `exclusions` - an array - A list of values to exclude from the final array output by this action.

Optional Params:
- None

Return Value: The filtered array.

#### take_first, take_last

These actions will expect an array as input, and will return either the first or the last element, respectively.

Required Params:
- None

Optional Params:
- None

Return Value: A single element from the input array to the action - either the first or last element, depending on which action is used.

#### take_head

This action is a synonym for take_first.

#### take_tail

This action will expect an array as input, and will return an array containing all element **except the first**.

Required Params:
- None

Optional Params:
- None

Return Value: The input array, with the first element removed. If a single- or zero-element array is passed as input, this action will return an empty array.


##### [Next Topic: Types are really just JSON Schema](./Types.md)

##### [Previous Topic: Creating your own Block types](./Creating_Block_Types.md)

### [Back to Table of Contents](../README.md)

