# Kragle.io Documentation

### [Table of Contents](../README.md)

##### [Previous Topic: Authentication](./Authentication.md)

##### [Next Topic: Types](./Types.md)

## API Reference - Block management

### GET /blocktypes

_(No authentication required for this endpoint)_

Retrieve a list of available Blocks for use when building stacks.

Pagination is supported via the `page` parameter.

You can also filter out non-public Blocks belonging to you and retrieve only public Blocks (including public blocks created by you) via the `filter_user_types` parameter (if true, your private Blocks will not be returned in the results).

_(Note that if the request is not authenticated, only public Blocks will be returned regardless of the value of the `filter_user_types` parameter.)_

Lastly, you can choose to not include the 'resolved' value of a Block - this means that for Blocks where `is_remote` is true, the returned Block data will include the uri of the Block but not the actual JSON value. This is via the `show_resolved` parameter. Note that if you choose to show resolved definitions, Blocks which are **not** remote will end up including their definitions twice in the result, once for 'definition' and once for 'resolved'.

The following is an example of the JSON structure returned by a successful request:
```
{
    "count": <total # of results based on query params>,
    "pagesize": <number of results per page>,
    "page": <current page of results in this response>,
    "blocktypes": [
        {
            "id": <id of the Block>,
            "owner": <id of the creator (owner) of this Block>,
            "name": <name of the Block>,
            "definition": <the Block definition - either a JSON string or a uri>,
            "is_remote": <boolean - true if this is a remote-style definition (definition is a uri)>,
            "needs_approval": <boolean - true if this Block has been submitted for approval, ie: pending being made public>,
            "is_approved": <boolean - true if the Block is public (has been approved for public use)>,
            "resolved": <the definition of the Block as a JSON string, regardless of is_remote>
        },
        ...
    ]
}
```

Note that `resolved` is only present if the `show_resolved` parameter is true.
Note that `is_approved` is only present if the `filter_user_types` parameter is false (when true, no non-public Blocks will be returned, hence you can safely assume any Block returned this way has `is_approved` == true).

**Required parameters**:
  - None

**Optional parameters**:
  - `page` - int - The page number of results you wish to retrieve. Default: 1.
  - `filter_user_types` - boolean - Whether to exclude private Blocks owned by the authenticated user. Default: false.
  - `show_resolved` - boolean - Whether to include a 'resolved' attribute for each Block returned. Default: false.

### GET /blocktypes/byid/<block id>

_(Calls to this endpoint for public Blocks do not require authentication. Calls to this endpoint for private Blocks require authentication. Unauthenticated calls for private Blocks will receive an HTTP 403 Forbidden response.)_

Retrieve information about a single Block, looked up by id.

Unlike retrieving a list of Blocks, there is no option to filter out 'user' Blocks (if the Block is visible to you, either publicly or because you own it, it will be returned) and there is no option for 'show_resolved' (the resolved definition is always included).

This endpoint returns a structure following this format:
```
{
    "id": <id of the Block>,
    "name": <name of the Block>,
    "owner": <id of the creator (owner) of this Block>,
    "is_remote": <boolean - true if this is a remote-style definition (definition is a uri)>,
    "is_approved": <boolean - true if the Block is public (has been approved for public use)>,
    "needs_approval": <boolean - true if this Block has been submitted for approval, ie: pending being made public>,
    "definition": <the Block definition - either a JSON string or a uri>,
    "resolved": <the definition of the Block as a JSON string, regardless of is_remote>
}
```

**Required  parameters**:
  - None

**Optional parameters**:
  - None

### GET /blocktypes/byname/<block name>

_(Calls to this endpoint for public Blocks do not require authentication. Calls to this endpoint for private Blocks require authentication. Unauthenticated calls for private Blocks will receive an HTTP 403 Forbidden response.)_

Retrieve information about a single Block, looked up by name.

Unlike retrieving a list of Blocks, there is no option to filter out 'user' Blocks (if the Block is visible to you, either publicly or because you own it, it will be returned) and there is no option for 'show_resolved' (the resolved definition is always included).

This endpoint returns a structure which follows the same format as the response from `GET /blocktypes/byid/<id>` above.

**Required  parameters**:
  - None

**Optional parameters**:
  - None

### POST /blocktypes

_(This endpoint **requires** authentication.)_

This endpoint is used to create a new Block.

The body of the request should be a JSON structure, which should be an object with top-level elements corresponding to the required and optional parameters described below.

On success, the id of the newly created Block is returned, in a JSON object like this:
```
{"id": <id of new Block>}
```

**Required parameters**:
  - `name` - string - The name of this Block.
  - `definition` - string - Either a url pointing to a JSON string, or a JSON string. In either case, the definition itself must be a valid Block structure. If this is a uri, you must also pass 'is_remote' and it must be true.

**Optional parameters**:
  - `is_remote` - boolean - Indicates whether the Block has a uri as it's definition, ie: the actual definition is hosted elsewhere. Required if passing a uri as 'definition', and in that case it must be true. Default: false.
  - `needs_approval` - boolean - Indicates whether the Block should be flagged for approval to be made public. Default: false.

### POST /validate/blocktypes

_(This endpoint **requires** authentication.)_

This endpoint is used to pre-validate a `POST /blocktypes` request before it's actually executed. It takes the same parameters and behaves the same way as the `POST /blocktypes` endpoint, with the only difference being the return value - instead of returning the id of the newly created block, since a block is **not** created when using this validation endpoint, the endpoint returns the validation status (true) if there was no error and the same request to `POST /blocktypes` ** _would have_ ** succeeded.

Here is an example of the return JSON from this endpoint:
```
{"validated": true}
```

### PUT /blocktypes/<id>

_(This endpoint **requires** authentication.)_

Used to modify an existing Block.

Like `POST /blocktypes`, the request body must be valid JSON.

The return value of a PUT operation for a Block is a json object representing the Block. An example follows:
```
{
    "id": 12345,
    "name": "My special Block",
    "owner": 54321,
    "is_remote": true,
    "is_approved": true,
    "needs_approval": false,
    "definition": "http://mydomain.com/myblock.json",
    "resolved": <JSON string containing the Block definition structure>
}
```

**Required parameters**:
  - None

**Optional parameters**:
  - `name` - string - The name of this Block.
  - `definition` - string - Either a url pointing to a JSON string, or a JSON string. In either case, the definition itself must be a valid Block structure. If this is a uri, you must also pass 'is_remote' and it must be true.
  - `is_remote` - boolean - Indicates whether the Block has a uri as it's definition, ie: the actual definition is hosted elsewhere. Required if passing a uri as 'definition', and in that case it must be true. Default: false.
  - `needs_approval` - boolean - Indicates whether the Block should be flagged for approval to be made public. Default: false.

##### [Next Topic: Types](./Types.md)

##### [Previous Topic: Authentication](./Authentication.md)

### [Back to Table of Contents](../README.md)

