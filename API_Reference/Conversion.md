# Kragle.io Documentation

### [Table of Contents](../README.md)

##### [Previous Topic: Validation](./Validation.md)

##### [Next Topic: Non-OAuth access management (HTTP Basic, etc)](./Access_Mgmt.md)

## API Reference - Conversion

### POST /util/convert

_(This endpoint does **not** require authentication.)_

This endpoint allows anyone to perform Kragle-style conversion of an arbitrary json structure against an arbitrary conversion specifier (see [Conversion Specifiers](../API-specific_Topics/Conversion_Specifiers.md) for details).

It is provided as a convenience for the community, users building custom Types/Blocks, etc.

The request body must be a JSON object following this format:
```
{
    "conversion": <object - the conversion specifier to use>,
    "data": <object to convert using the conversion specifier>
}
```

The response body will be a JSON object - the result of the conversion.

**NB: You can also pass _uri -appended versions of both the `schema` and `data` parameters if you with to pass a url which points to an appropriate object, instead of the object itself. Ie: this is also valid:**

```json
{
    "conversion_uri": <string - a url where a JSON object representing the conversion specifier may be downloaded>,
    "data_uri": <string - a url where a JSON object representing your data to be converted may be downloaded>
}
```

##### [Next Topic: Non-OAuth access management (HTTP Basic, etc)](./Access_Mgmt.md)

##### [Previous Topic: Validation](./Validation.md)

### [Back to Table of Contents](../README.md)

