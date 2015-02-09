# Kragle.io Documentation

### [Table of Contents](../README.md)

##### [Previous Topic: Schedules](./Schedules.md)

##### [Next Topic: Conversion](./Conversion.md)

## API Reference - Validation

### POST /util/validate

_(This endpoint does **not** require authentication.)_

This endpoint allows anyone to perform validation of an arbitrary json structure against an arbitrary JSON Schema.

It is provided as a convenience for the community, users building custom Types/Blocks, etc.

The `schema` portion of the request can either be a JSON object which **is** the schema, or a string pointing to a url where a JSON object (which is a valid Schema) can be retrieved.

The request body must be a JSON object following this format:
```json
{
    "schema": <object - a JSON schema>,
    "data": <object to validate against the schema>
}
```

The response body will be a JSON object in the following format:
```json
{
    "status": <0 for success, or 1 for failure>
}
```

**NB: You can also pass _uri -appended versions of both the `schema` and `data` parameters if you with to pass a url which points to an appropriate object, instead of the object itself. Ie: this is also valid:**

```json
{
    "schema_uri": <string - a url where a valid JSON schema may be downloaded>,
    "data_uri": <string - a url where your JSON object to be validated may be downloaded>
}
```

##### [Next Topic: Conversion](./Conversion.md)

##### [Previous Topic: Schedules](./Schedules.md)

### [Back to Table of Contents](../README.md)

