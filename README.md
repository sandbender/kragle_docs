# Kragle.io Documentation
Documentation for the Kragle.io api and site/service.

### Table of Contents

1. [What is Kragle?](./What_is_Kragle.md)
1. [How it works](./How_it_works.md)
1. [The Basics](../../tree/master/The_Basics)
  1. [Stacks](./The_Basics/Stacks.md)
  1. [Blocks](./The_Basics/Blocks.md)
    1. Access/Authentication - How Kragle talks to your apps
    1. Types
  1. [Jobs](./The_Basics/Jobs.md)
  1. [Sharing Stacks](./The_Basics/Sharing_Stacks.md)
1. Advanced Topics
  1. Creating your own Block types
    1. Sharing your custom Block types
    1. Remote types (ie: definitions hosted elsewhere)
    1. The Block Type schema
    1. Specifying input and output Types
      1. Using Types which are valid for the web
    1. Actions requiring authentication/credentials
    1. Actions
    1. State
      1. Specifying initial state
  1. Actions available for Block types
      1. uri_get, uri_head, uri_post
        1. _with_headers variants
      1. convert
      1. *more to come*
  1. Types are really just JSON Schema
    1. The Type schema
      1. The GUI Type schema
    1. The Block schema
  1. Creating your own Types
    1. Sharing your custom Types
    1. Remote types (ie: definitions hosted elsewhere)
  1. Webhooks and Manually Triggered Stacks
1. API-specific Topics
  1. Conversion specifiers
    1. Source References
      1. Referencing arrays in the source
      1. Embedded Source References for strings
  1. Conversion Functions
    1. TIMESTAMP
    1. URLENCODE
    1. URLENCODE_PLUS
    1. *more to come*
1. API Reference
  1. Login
  1. Blocks
  1. Types
  1. Stacks
  1. Jobs
  1. Schedules
  1. Validation
  1. Conversion
  1. Triggering manual stacks (webhooks)
  1. Non-OAuth access management (HTTP Basic, etc)

