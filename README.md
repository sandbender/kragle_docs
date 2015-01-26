![Kragle.io Logo](http://bit.ly/1EgMjSQ "Kragle.io Logo")

# Kragle.io Documentation

### Table of Contents

1. [What is Kragle?](./What_is_Kragle.md)
  1. Explanation
  1. Examples
  1. FAQ
1. [How it works](./How_it_works.md)
  1. A Tiny bit of terminology
  1. Creating "Stacks"
  1. Creating "Jobs"
1. [The Basics](../../tree/master/The_Basics)
  1. [Stacks](./The_Basics/Stacks.md)
  1. [Blocks](./The_Basics/Blocks.md)
    1. Access/Authentication - How Kragle talks to your apps
  1. [Jobs](./The_Basics/Jobs.md)
  1. [Sharing Stacks](./The_Basics/Sharing_Stacks.md)
1. [Limits](./Limits.md)
1. [Available Blocks](./Available_Blocks.md)
1. [Available Types](./Available_Types.md)
1. [Advanced Topics](../../tree/master/Advanced_Topics)
  1. [Creating your own Block types](./Advanced_Topics/Creating_Block_Types.md)
    1. Sharing your custom Block types
    1. Remote types (ie: definitions hosted elsewhere)
    1. The Block Type schema
    1. Specifying input and output Types
      1. Using Types which are valid for the web
    1. Actions
      1. The Action input structure
      1. Actions requiring authentication/credentials
    1. State
      1. Specifying initial state
  1. [Actions available for Block types](./Advanced_Topics/Actions_for_Block_Types.md)
      1. uri_get, uri_head, uri_post
        1. _with_headers variants
      1. convert
      1. *more to come*
  1. [Types are really just JSON Schema](./Advanced_Topics/Types.md)
    1. The Type schema
      1. The GUI Type schema
    1. The Block schema
  1. [Creating your own Types](./Advanced_Topics/Creating_Types.md)
    1. Making Types flexible - re-using existing Types (schema) and extending/generalizing/etc.
    1. Sharing your custom Types
  1. [Webhooks and Manually Triggered Stacks](./Advanced_Topics/Webhooks.md)
1. [Links (Relevant external docs, ie: JSON Schema, etc)](./Links.md)
1. [API-specific Topics](../../tree/master/API-specific_Topics)
  1. [Conversion Specifiers](./API-specific_Topics/Conversion_Specifiers.md)
    1. Basic usage
    1. Order of replacements (inline vs. regular)
    1. Referencing functions instead of data
    1. Nesting replacement markers?
    1. Referencing array indices within replacement markers
    1. Converting arrays in source data to arrays in the output structure
    1. Local vs root references to source data
  1. [Conversion Functions](./API-specific_Topics/Conversion_Functions.md)
    1. TIMESTAMP
    1. URLENCODE
    1. URLENCODE_PLUS
    1. *more to come*
1. [API Reference](./API_Reference.md)
  1. [Authentication](./API_Reference/Authentication.md)
  1. [Blocks](./API_Reference/Blocks.md)
  1. [Types](./API_Reference/Types.md)
  1. [Stacks](./API_Reference/Stacks.md)
  1. [Jobs](./API_Reference/Jobs.md)
  1. [Schedules](./API_Reference/Schedules.md)
  1. [Validation](./API_Reference/Validation.md)
  1. [Conversion](./API_Reference/Conversion.md)
  1. [Triggering manual stacks (webhooks)](./API_Reference/Webhooks.md)
  1. [Non-OAuth access management (HTTP Basic, etc)](./API_Reference/Access_Mgmt.md)
  1. [Faults and Error Codes](./API_Reference/Faults.md)

