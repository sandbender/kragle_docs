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
```
{
    "schema": <object or JSON string, either of which must be a valid JSON Schema>,
    "data": <object to validate against the schema>
}
```

The response body will be a JSON object in the following format:
```
{
    "status": <0 for success, or 1 for failure>
}
```

##### [Next Topic: Conversion](./Conversion.md)

##### [Previous Topic: Schedules](./Schedules.md)

### [Back to Table of Contents](../README.md)

