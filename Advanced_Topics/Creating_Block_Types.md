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

### How to create a custom Block type

You must use Kragle's JSON API in order to create your own block types. The 'definition' element of the object you POST to the blocktypes endpoint to create a block describes the various elements that make up a Block type. The Definition of a Block type must contain:

1. An element called 'io' which indicates what Types of data a Block accepts and produces,
1. An element called 'actions' which specifies the list of actions to be performed by the block,
1. An element called 'state' which specifies the data to be saved on each execution of the block, and
1. An element called 'initial_state' which is used to initialize an individual Block's state when it is created as part of a Stack

In addition to the 'definition' of a Block, you'll also need to specify a few other pieces of information for your new Block:

1. The 'name' of your block - this needs to be unique.
1. Optional: 'is_remote' is a boolean indicating that the 'definition' is a url where the actual definition can be fetched.
1. Optional: 'needs_approval' is a boolean indicating that you wish to flag (if true) the custom Block for review and public availability.

### Input and Output types for a Block type definition

All Block types must specify the input and output Types which they expect/produce. There are a number of different reasons for
this but the most basic/obvious is so that the system, when connecting Blocks into a Stack, knows how to perform translation
between the kind of data one Block produces, and the kind of data a connected Block expects.

Types are specified by their Kragle name, which in turn corresponds to a JSON Schema describing the format of the data
structure for that Type. In a Block 'definition' structure, the input and output types are specified in the 'io' top-level element of the 'definition'. The 'io' element is an object with two elements, 'input' and 'output', which are described below.

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

Actions are the heart of a Block - essentially a Block is just a sequence of Actions, with some added functionality to support input and output validation, state information, etc.

Each action receives input conforming to a specific structure, performs a single action, and (possibly) outputs some data.

The most common Actions you'll use all the time when building custom Block types are uri-oriented action (uri_get, uri_post, etc) and the 'convert' action, which you'll use to transform the 'raw' response from a url/api endpoint into the appropriate structure for a Kragle Type. (*The 'convert' action is very useful and comes into play in a few other places as well, most notably when using the API to create your Stacks.*)

The Actions which make up a Block are specified as an ordered list (array), and each different action type available will have it's own set of required and optional parameters it needs to be configured with when using it to build a Block. The list of Actions for a block is specified as the 'actions' top-level element of your block definition structure. Each element in the 'actions' list must be an object having two elements: 'action' (the name of the action to use) and 'action_params' (an object whose elements are the required/optional parameters for the action in question).

#### The Action input structure

You will often want or need to use data present in the input to a Block in various spots in your actions. This is supported via the
'convert' process/action mentioned above. When specifying the parameters for an Action, you can use special placeholders which reference
input data, which will be automatically replaced at runtime through the 'convert' mechanism.

Full details on how the 'convert' mechanism works are provided later under the API-specific Topic [Conversion Specifiers](../API-specific_Topics/Conversion_Specifiers.md). For now, we'll describe the input structure that every Action can reference via these placeholders:

```json
{
    "input": <THE INPUT>,
    "input_state": <INPUT STATE FROM THE LAST RUN>,
    "output_state": <OUTPUT STATE FROM THE LAST RUN>
```

##### THE INPUT

This element corresponds to whatever data structure the previous Block in the STack (if this is the first Action in a Block) or previous Action in the Block (all subsequent Actions in the Block) produced as output.

##### INPUT STATE FROM THE LAST RUN

This element will correspond to the 'input' state structure which was saved the last time this Block **instance** executed. Note that there's a difference between an instance of a Block (ie: a "copy" of a Block that is part of an active Stack) and the Block itself (a type which is not specific to a stack/etc).

See the State section below for more details.

##### OUTPUT STATE FROM THE LAST RUN

Like the corresponding 'input_state' element, 'output_state' provides the data structure saved as the output state from the last time this Block **instance** executed.

#### Actions requiring authentication/credentials

Some Actions (typically the uri_* family of Actions) may require authentication or access credentials (username/password) depending on
the uri you specify. In these cases, you will be able to provide the basic details as parameters to those actions. The two required parameters for auth to work are 'auth_required' (a boolean, ie: either true or false) and 'auth_type' (one of the predefined types, currently only 'basic' and 'strategy' are supported).

The 'strategy' auth type indicates that Kragle should use an OAuth strategy to access this url. It is **not** required that you provide
any other details - Kragle has a pluggable auth system and will automatically figure out the mechanism required to obtain the appropriate access token(s)/etc. If you specify a url for which we do not already have explicit support, it will be added - the pluggable auth system makes this fast, and we can (and **will**) typically add the needed support within 24 hours of you creating the Block which uses this auth type (we are automatically notified whenever a new Block type is created which requires new auth support).

The 'basic' auth type indicates HTTP Basic authorization should be used - you will be able to provide your Basic auth creds for this uri separately via an API endpoint which provides the ability to manage your auth credentials for various uris.

**NB**: HTTP Basic auth is highly discouraged - the vast majority of public sites & services today use some variant of OAuth - much more secure and configurable access-wise than something simple like HTTP Basic.

### State

You will often want to save data in between executions of a Stack. For example, when searching for hashtag results from Twitter you will likely only want results that are new since the last time the Stack in question was run, and so you may want to save the post ID of the most recent tweet your stack has seen and use that to restrict your results next time.

Kragle supports saving such State data in between executions of a Block/Stack.

State is saved on a per-Block-instance basis, so you could theoretically have two copies of the same Block type within a single Stack, and each would save it's own state data separately.

The 'state' structure (a top level element of your Block's 'definition' structure) is used to specify what data from the input or output of the block you wish to save for use during the next execution of the Block. ('state' structure is described below)

Just like action parameters, the State structure you specify uses the 'convert' mechanism to replace placeholders in the structure with corresponding data from the input or output of a block.

Any placeholders in the 'input' section of your state structure will be replaced with the corresponding elements from whatever structure was the 'raw' input to the block. Likewise, any placeholders in the 'output' section of your state structure will be replaced with the corresponding elements from whatever data structure was output by this block.

An example will be helpful here. Let's assume the following imaginary structure represents Twitter hashtag search results:

```json
{
    "tweets": [
        {"body": "this is a twitter post (tweet) #cats", "owner_screen_name": "SandbenderCa"},
        {"body": "Look, a cat picture! bit.ly/ABCDE #cats", "owner_screen_name": "SandbenderCa"}
    ],
    "last_tweet_id_in_results": 1234567
}
```

We could save the id of the last tweet, for use next time in the params for our uri_get Action which retrieves such results, by
specifying the following State structure in the Block definition:

```json
{
    "input": {},
    "output": {
        "last_tweet_id": <'convert' placeholder pointing to last_tweet_id_in_results>
    }
}
```

In which case, the next time this Block instance ran, we would get something like this as the input structure to the same Action:

```json
{
    "input": <some input>,
    "input_state": {},
    "output_state": {"last_tweet_id": 1234567}
}
```

... which we could then use when crafting the uri for this Action that will pull **recent** hashtag search results, etc.

#### Specifying initial state

When creating custom Blocks that use State, you will need to specify the initial state for the Block which will be used the first
time the block runs, so that there is data there for it to reference when it looks for the relevant pieces of State.

This is done with the 'initial_state' element of a Block definition... 'initial_state' is a **static** data structure which is saved as the starting state for a Block every time the block is used in a Stack. The 'initial_state' element of a block definition must be an object, have 'input' and 'output' properties. Each of 'input' and 'output' must be objects themselves, and their contents can be anything.

State always over-writes itself on every Block execution, so the initial state is **only** used/relevant the very first time an
instance of the Block is executed within a Stack.

##### [Next Topic: Actions available for Block types](./Actions_for_Block_Types.md)

##### [Previous Topic: Available Blocks](../Available_Blocks.md)

### [Back to Table of Contents](../README.md)

