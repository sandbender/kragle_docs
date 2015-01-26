# Kragle.io Documentation

### [Table of Contents](../README.md)

##### [Previous Topic: Types are really just JSON Schema](./Types.md)

##### [Next Topic: Webhooks and Manually Triggered Stacks](./Webhooks.md)

## Creating your own Types

A custom Type 'definition' is simply a JSON structure which validates as a JSON Schema. (See the previous section for a discussion of how JSON Schema are used in this way). Like Blocks, Types also require a unique 'name'.

### Making Types flexible - re-using existing Types (schema) and extending/generalizing/etc.

In order to encourage collaboration between users and within the Kragle community, when creating your custom Types (assuming they will be shared publicly), you should make every effort to ensure that your Types are as re-usable as possible. The following guidelines offer some advice:

1. **Use existing Types whenever possible.** If you are designing a custom Block, and are creating a custom Type to go along with it, you should make every effort to ensure that there is not already an appropriate public Type that you can use instead. If so, consider using the existing Type instead of creating a new one!
1. **When creating a new Type, _extend_ an existing Type if possible**. What this means is that if you are creating a new Type which is similar to an existing type in terms of the data it contains (for example), except that you are adding a new piece of data, try to create your Type schema in such a way that your new (extended) Type will also validate as an instance of the existing Type.
1. **WHen creating a new Type, _generalize_ and existing Type if possible**. Along the same lines as extending Types, if your new Type is similar to an existing Type except it contains **less** data, try to create your schema such that instances of the existing Type will also validate as instances of your new Type.

These rules are not hard-and-fast, nor are they required - they are simply guidelines intended to make cross-usage of Types and Blocks easier... the closer your Type resembles existing Types, and validates as existing Types or vice-versa, the less custom conversion people will need to write when using your Type.

### Sharing your custom Types

Like custom Blocks, custom Types are private by default, but can be shared (made public for other Kragle users to take advantage of). Like Blocks, we highly encourage you to make any non-sensitive Types you create public so that others will be able to use them.

The main reason for needing to share your Types is so that others will be able to use your Blocks that take the custom Type as input or output. If a custom Block is public, but it uses Types which are **not** public, it will be unusable to others (and unavailable, despite being flagged as public) since the Types it uses will be unknown to anyone other than the owner of the Type.

So, the rule of thumb is: **Always** make custom Types public _unless_ they are **only** used by private, custom Blocks.

##### [Next Topic: Webhooks and Manually Triggered Stacks](./Webhooks.md)

##### [Previous Topic: Types are really just JSON Schema](./Types.md)

### [Back to Table of Contents](../README.md)

