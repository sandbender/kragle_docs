# Kragle.io Documentation

### [Table of Contents](./README.md)

##### [Previous Topic: Available Blocks](./Available_Blocks.md)

##### [Next Topic: Advanced Topics](../../tree/master/Advanced_Topics)

## Available Types

This is a list of publicly available Types currently available for use when creating custom Blocks (ie: for use when specifying input and output Types.

We will attempt to keep this up to date, but the main goal is to provide documentation for **some** Types, so that users of
this documentation can get a 'feel' for how they work/what they look like/how they're created/etc.

You can always use the site to lookup Types, or pull their details via the public API.

The following list is broken up according to site/service/app which the Type in question is related to, as most Types will be site-specific (ie: a Type for a Tweet would be Twitter-specific, a Pin would be Pinterest-specific, etc).

The first 'Misc' category below collects some well-known Types which are **not** site-specific.

### Misc

### Delicious

#### <a name='DeliciousTagSearch'></a>Delicious Tag Search

A Type representing the parameters required to search your recent Delicious posts by tag.

Contains a single element, 'tag', whose value is the name of the tag used to search posts.

Type Schema: [Delicious Tag Search](https://github.com/sandbender/json_schema/raw/master/data_type__delicious_tag_search.json)

#### <a name='DeliciousPost'></a>Delicious Post

A Type representing a single saved post on Delicious.

Contains 'description', 'url', 'tags' and 'hash' (fields should be self-explanatory).

Type Schema: [Delicious Post](https://github.com/sandbender/json_schema/raw/master/data_type__delicious_post.json)

### HipChat

#### <a name='HipChatNewMessageforRoom'></a>HipChat - New Message for Room

A Type representing a new message suitable for sending to a HipChat room.

Contains 'room_id', 'auth_token' and 'content', where 'room_id' is the HipChat api id of the room to which the message should be sent, 'auth_token' is a Notification-style HipChat token for the room in question, and 'content' is the message to be sent.

Type Schema: [HipChat - New Message for Room](https://github.com/sandbender/json_schema/raw/master/data_type__hipchat_message_to_room.json)

##### [Next Topic: Advanced Topics](../../tree/master/Advanced_Topics)

##### [Previous Topic: Available Blocks](./Available_Blocks.md)

### [Back to Table of Contents](./README.md)
