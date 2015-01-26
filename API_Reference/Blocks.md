# Kragle.io Documentation

### [Table of Contents](../README.md)

##### [Previous Topic: Authentication](./Authentication.md)

##### [Next Topic: Types](./Types.md)

## API Reference - Block management

### GET /blocktypes

Retrieve a list of available Blocks for use when building stacks.

Pagination is supported via the `page` parameter.

You can also filter out non-public Blocks belonging to you and retrieve only public Blocks (including public blocks created by you) via the `filter_user_types` parameter (if true, your private Blocks will not be returned in the results).

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

Required parameters:
None

Optional parameters:
page - int - The page number of results you wish to retrieve. Default: 1.
filter_user_types - boolean - Whether or not to include private Blocks owned by the authenticated user. Default: false.
show_resolved - boolean - Whether to include a 'resolved' attribute for each Block returned. Default: false.

### GET /blocktypes/byid/<block id>


##### [Next Topic: Types](./Types.md)

##### [Previous Topic: Authentication](./Authentication.md)

### [Back to Table of Contents](../README.md)

