# Kragle.io Documentation

### [Table of Contents](../README.md)

##### [Previous Topic: Types](./Types.md)

##### [Next Topic: Jobs](./Jobs.md)

## API Reference - Stack management

### GET /blockstacks

_(No authentication is required for this endpoint.)_

This endpoint is used to retrieve a list of publicly viewable Stacks. Stacks are usually made public so that others in the community can get ideas for what they can do with Kragle, or so they can copy an existing Stack they want to use themselves, see how Stacks are built, etc.

This endpoint will return public Stacks owned by others, and your own public Stacks. If the request is authenticated, it will also return your own private Stacks.

Pagination is supported via the `page` parameter.

The following is an example of the JSON structure returned by a successful request:
```
{
    "count": <total # of results based on query params>,
    "pagesize": <number of results per page>,
    "page": <current page of results in this response>,
    "stacks": [
        {
            "id": <id of the Block>,
            "owner": <id of the creator (owner) of this Block>,
            "name": <name of the Block>,
            "webhook": <null, or one of the predefined webhook types>,
            "webhook_method": <GET or POST>,
            "webhook_token": <token for this webhook, or null>,
            "blocks": [
                {"id": 123, "type": "name of Block", "conversion": {}, "state": {"input": {}, "output": {}}},
                ...
            ],
            "jobs": [
                {"id": 321, "name": "name of Job", "do_run": true, "schedule": {<schedule config for job>}},
                ...
            ]
        },
        ...
    ]
}
```

**NB:** the `conversion` and `state` elements of each Block in the stack within the return structure above, as well as the `jobs` and `webhook_token` elements, are **ONLY** included for Blocks which you own, and only if the request is authenticated. The following is an example of a successful reponse to a **non**-authenticated request:

```
{
    "count": <total # of results based on query params>,
    "pagesize": <number of results per page>,
    "page": <current page of results in this response>,
    "stacks": [
        {
            "id": <id of the Block>,
            "owner": <id of the creator (owner) of this Block>,
            "name": <name of the Block>,
            "webhook": <null, or one of the predefined webhook types>,
            "webhook_method": <GET or POST>,
            "blocks": [
                {"id": 123, "type": "name of Block"},
                ...
            ]
        },
        ...
    ]
}
```

**Required parameters**:
  - None

**Optional parameters**:
  - `page` - integer - The page number from the full result set you wish to retrieve. Default: 1.

### GET /blockstacks/byid/<stack id>
### GET /blockstacks/byname/<stack name>

_(Calls to this endpoint for public Stacks do not require authentication. Calls to this endpoint for private Stacks require authentication. Unauthenticated calls for private Types will receive an HTTP 403 Forbidden response.)_

Retrieve information about a single Stack, looked up by id or name.

This endpoint returns a structure following this format (representing the Stack in question):
```
{
    "id": <id of the Block>,
    "owner": <id of the creator (owner) of this Block>,
    "name": <name of the Block>,
    "webhook": <null, or one of the predefined webhook types>,
    "webhook_method": <GET or POST>,
    "webhook_token": <token for this webhook, or null>,
    "blocks": [
        {"id": 123, "type": "name of Block", "conversion": {}, "state": {"input": {}, "output": {}}},
        ...
    ],
    "jobs": [
        {"id": 321, "name": "name of Job", "do_run": true, "schedule": {<schedule config for job>}},
        ...
    ]
}
```

**NB:** The `webhook_token` and `jobs` elements, as well as the `conversion` and `state` elements of each Block in the stack, are **ONLY** returned for Stacks you own, and only when the request is authenticated. For **Unauthenticated** requests, the structure of the response will follow this format:

```
{
    "id": <id of the Block>,
    "owner": <id of the creator (owner) of this Block>,
    "name": <name of the Block>,
    "webhook": <null, or one of the predefined webhook types>,
    "webhook_method": <GET or POST>,
    "blocks": [
        {"id": 123, "type": "name of Block"},
        ...
    ],
}
```

**Required parameters**:
  - None

**Optional parameters**:
  - None

### POST /blockstacks

_(This endpoint **requires** authentication.)_

This endpoint it used to create a new Stack.

The body of the request should be a JSON structure, which should be an object with top-level elements corresponding to the required and optional parameters described below.

On success, the id of the newly created Stack is returned in a JSON object like this:
```
{"id": <id of new Stack>}
```

**Required parameters**:
  - `name` - string - The name for this Stack - something descriptive!
  - `blocks` - array - An ordered list of Blocks from which to build the Stack. Each Block should be represented as an array, with elements corresponding to the following indexes:
    - **Required indexes**:
      - `0` - string - The first element in a 'Block array' for a new Stack must be the name of the Block to use.
      - `1` - <anything> - The next element must be a conversion specifier, indicating how to translate the input this Block will receive into the Type it expects, as defined by the 'io["input"]' element of the definition for this kind of Block.
    - **Optional indexes**:
      - `2` - object - An object containing zero or more of the following optional parameters for the new instance of this block:
        - `name` - string - An identifier for this particular instance of the Block (ie: specific to **this** Block in **this** Stack.
        - `state` - object - An object containing `input` and `output` elements, each of which represents State for the block (to be used in place of the default 'initial_state' for the Block when it is created.

**Optional parameters**:
  - `schedule_id` - integer - The id of a Schedule object with which to create an initial Job for the new Stack. Default: Do not create an initial Job for the Stack.
  - `enabled` - boolean - Whether or not to enable the new Stack once created. Default: false (only makes sense in conjunction with `schedule_id`).
  - `needs_approval` - boolean - Whether to flag the Stack as needing approval to be made public. Default: false.
  - `webhook` - string - One of (null, "public", "token", or "user"). Optionally set the new Stack for webhook-style access. Default: null.
  - `webhook_method` - string - GET or POST. Only makes sense w/ `webhook`, if `webhook` is non-null.

Sample JSON request body:
```
{
    "name": "Everything is Awesome",
    "schedule_id": 432,
    "enabled": true,
    "needs_approval": false,
    "webhook": "token",
    "webhook_method": "POST",
    "blocks": [
        ["Post to Twitter", {"tweet_body": "\u0001/input/screen_name\u0002 says #gluetogetherallt3hthings!"}],
    ]
}
```

### PUT /blockstacks/<stack id>

_(This endpoint **requires** authentication.)_

Used to modify an existing Stack.

Like `POST /blockstacks`, the request body must be valid JSON.

The return value of a PUT operation for a Stack is a json object representing the Stack. An example follows:
```
{
    "id": <id of Stack>,
    "owner": <id of user owning this Stack>,
    "name": <name of the Stack>,
    "is_approved": false,
    "needs_approval": false,
    "webhook": "user",
    "webhook_method": "GET",
    "webhook_token": "1234567890ABCDEF",
    "instances": [<block instance id>, ...],
    "jobs": [<job id>, ...]
}
```

**Required parameters**:
  - None

**Optional parameters**:
  - `name` - string - The name for this Stack - something descriptive!
  - `blocks` - array - An ordered list of Blocks from which to build the Stack. Each Block should be represented as an array, with elements corresponding to the following indexes:
    - **Required indexes**:
      - `0` - string - The first element in a 'Block array' for a new Stack must be the name of the Block to use.
      - `1` - <anything> - The next element must be a conversion specifier, indicating how to translate the input this Block will receive into the Type it expects, as defined by the 'io["input"]' element of the definition for this kind of Block.
    - **Optional indexes**:
      - `2` - object - An object containing zero or more of the following optional parameters for the new instance of this block:
        - `name` - string - An identifier for this particular instance of the Block (ie: specific to **this** Block in **this** Stack.
        - `state` - object - An object containing `input` and `output` elements, each of which represents State for the block (to be used in place of the default 'initial_state' for the Block when it is created.
  - `schedule_id` - integer - The id of a Schedule object with which to create an initial Job for the new Stack. Default: Do not create an initial Job for the Stack.
  - `enabled` - boolean - Whether or not to enable the new Stack once created. Default: false (only makes sense in conjunction with `schedule_id`).
  - `needs_approval` - boolean - Whether to flag the Stack as needing approval to be made public. Default: false.
  - `webhook` - string - One of (null, "public", "token", or "user"). Optionally set the new Stack for webhook-style access. Default: null.
  - `webhook_method` - string - GET or POST. Only makes sense w/ `webhook`, if `webhook` is non-null.

##### [Next Topic: Jobs](./Jobs.md)

##### [Previous Topic: Types](./Types.md)

### [Back to Table of Contents](../README.md)

