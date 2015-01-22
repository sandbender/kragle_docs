# Kragle.io Documentation

### [Table of Contents](../README.md)

##### [Previous Topic: Available Blocks](../Available_Blocks.md)

##### [Next Topic: Actions available for Block types](./Actions_for_Block_Types.md)

## Creating your own Block types

### Sharing your custom Block types

Like Stacks, when you create your own Block types they can also be shared with other users (made public on Kragle).

Again, we value collaboration and community innovation very highly - the service would be fairly boring if we limited
you to only using the Block types we provide/add support for - we want all our users to imagine new ways of using Kragle,
and empower them to create those uses if they're not already available... so that's what we did :)

Why create your own Block type? The possibilities are endless but at a very basic level, a good example would be connecting
your Kragle Stacks to a site/service/app for which we don't already have support. In Kragle, this is possible, and we've tried
to make it as easy as possible. In this case, all you would need to do is look at the Block type definition for an existing
Block that talks to a url, copy that and modify it with the url of the new site you want to connect to, and save it.

Custom Block types are private by default, but you can flag them as shareable/public if you want others to be able to use them.

In most cases, you will **want** to make them public - the only reason private is the default is so that you can safely
create Block types which talk to private services (your own server, as an example) without worrying about others using the new
Block as well and talking to your stuff accidentally/etc.

When you mark a custom Block type as public, we'll get a notification letting us know that there's an approval pending - this
process just takes a minute on our end and is a safety feature - we just want to make sure you don't accidentally share a
Block containing personal/sensitive information, or one which has malicious intent if used by someone else, etc. Assuming
there's no issue with your custom type, we'll approve the public-request and anyone else using Kragle will then be able to see
and use your custom Block type.

### Remote Block type definitions

When developing your own Block types, it's often useful to keep your working copies/etc in a code repo somewhere. Kragle makes
this even easier by supporting 'remote' types for both Blocks and Types (more on Types later).

Any **public** repository which you can use to link directly to 'raw' files is usable - GitHub, Bitbucket, etc.

Simply mark the Block type as remote (by setting the is_remote property of it to True), and provide the url to the defintion
of the Block type instead of the definition itself (a json string). Our system will fetch and cache the actual definition
from your repository.

**NB**: Custom Blocks which are made public become read-only, ie: they can't be modified once they're flagged as public. This
applies to remote definitions as well - once a Block type is made public, we will cease to allow updates and will no longer
fetch fresh versions of your definition from the url, even if you change the definition.

### The Block type schema (JSON Schema)

Kragle relies 

##### [Next Topic: Actions available for Block types](./Actions_for_Block_Types.md)

##### [Previous Topic: Available Blocks](../Available_Blocks.md)

### [Back to Table of Contents](../README.md)

