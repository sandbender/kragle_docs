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

Kragle relies heavily on JSON Schema for validation different things.

One such usage is to validate a custom Block type definition. We'll discuss the individual pieces that make up a Block type
definition in the sections below, but the complete JSON Schema against which a Block type definition must validate is located
here: https://raw.githubusercontent.com/sandbender/json_schema/master/block_type.json

### Input and Output types for a Block type definition

All Block types must specify the input and output Types which they expect/produce. There are a number of different reasons for
this but the most basic/obvious is so that the system, when connecting Blocks into a Stack, knows how to perform translation
between the kind of data one Block produces, and the kind of data a connected Block expects.

Types are specified by their Kragle name, which in turn corresponds to a JSON Schema describing the format of the data
structure for that Type.

Blocks can receive either a single instance of a Type of data, or an array of the given Type (or nothing) as input.

If a Block is specified as having nothing (null) as it's input, then it **ignores** any input which may or may not be present.
This means that any data which the previous Block potentially produces is ignored, and will **not** be validated as anything is
theoretically valid. Also, this means that you should **not** reference any input data in the actions of the block definition, 
since the input is not validated and therefore any referenced input may not be present when an instance of the Block runs.

Likewise, Blocks can produce either a single Type, an array of instances of a Type, or nothing (null).

Treatment of null as the output type works the same way as input - any output which may be produced is not validated and therefore
"unsafe" for usage in subsequent blocks/etc.

Generally speaking you'll always want to specify non-null input and output types - the only time you'll want to use null is
when a Block does not need any input, or when it actually produces nothing.

#### Using input Types which are valid for the web

If you are creating a block that you want to share and allow others to use on the web (ie: through the Kragle.io site/app), the
Type you specify must themselves validate against a restricted JSON Schema - basically, a Type used as input for a Block must not
contain any arrays - only regular (nested is ok) objects or scalars.

The JSON Schema against which a web-usable Type must validate is located here: https://raw.githubusercontent.com/sandbender/json_schema/master/data_type_gui.json

Note that this does **not** apply to API usage - any Type can be usable as input to a Block when that Block is being used through the Kragle.io api to create stacks/etc, since API-created stacks must explicitly provide a conversion "map" for input and therefore are not encumbered by UI/UX restrictions which are the reason for this rule.

### Actions



##### [Next Topic: Actions available for Block types](./Actions_for_Block_Types.md)

##### [Previous Topic: Available Blocks](../Available_Blocks.md)

### [Back to Table of Contents](../README.md)

