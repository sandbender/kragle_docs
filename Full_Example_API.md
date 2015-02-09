# Kragle.io Documentation

### [Table of Contents](./README.md)

##### [Previous Topic: Faults and Error Codes](./API_Reference/Faults.md)

## A Full Example/Walkthrough, via the API

**NB:** All the following code is written with Python 2.7 and utilizes the 'requests' and 'json' libraries. However all the examples should be easily adaptable to any other language with support for basic HTTP requests and json encoding/decoding.

In this example, we'll create a connection between a Delicious.com account and a Hipchat room.

We want to have Kragle post a message to the Hipchat room, which contains some of our coworkers, whenever we post a new url to Delicious that's tagged with "coworkers".

### Creating Stacks vs. Custom Blocks and Types

There are two parts to this walkthrough - the first involves creating custom Blocks and associated Types, when the action you want does not already exist as a Block in Kragle. The second part involves using Blocks (whether pre-existing or your own freshly created custom Blocks) to make a Stack that does what we want.

### Starting off - Authentication

Before we do anything else, we will need to authenticate against the API to get an auth ticket cookie we can use for subsequent requests, since many of the endpoints we will call require authenticated requests:

```python
>>> import requests
>>> import json
>>>
>>> body_headers = {'content-type': 'application/json'}
>>>
>>> login_payload = {'username': 'SandbenderCa', 'password': 'itsapassword'}
>>>
>>> response = requests.post('https://kragle.io/api/v1/authenticate', headers=body_headers, data=json.dumps(login_payload))
>>>
>>> cookies = response.cookies.get_dict('kragle.io')
>>> cookies
{'auth_tkt': '"11111111111111122222222222222222222223333333333333334444444444444445555555555555555666666666666666667777777777777788888888888888999990000!userid_type:int"'}
```

We have also setup a dict called `body_headers` which we will use in later requests that send JSON data to the endpoint, and once authenticated successfully, we receive an `auth_tkt` cookie which we can pass to subsequent requests.

**If you are only interested in creating Stacks from predefined Blocks, you can skip down to the second section titled [Creating a Stack](#stack_creation). Otherwise, read on for an introduction to creating your own Kragle functionality with custom Blocks and Types.**

### Listing available Blocks

When browsing Blocks through the API, we can use the "GET /blocktypes" endpoint to list any available Blocks that we can use in our Stack creations. In the case of our Delicious -> HipChat example, we will need a few different Blocks: one to retrieve recent Delicious posts that have a certain tag, and one to send a message to a HipChat room.

Here is a sample request to the "GET /blocktypes" endpoint:

```python
>>> requests.get('https://kragle.io/api/v1/blocktypes?show_resolved=1', cookies=cookies).json()
{'count': 1,
 'page': 1,
 'pagesize': 10,
 'blocktypes': [
     {'id': 2,
      'name': 'Last Post on Delicious tagged...',
      'is_remote': True,
      'definition': 'https://raw.githubusercontent.com/sandbender/json_schema/master/block__delicious_tag_results_last.json',
      ...,
      'resolved': {'io': {'input': 'Delicious Tag Search', 'output': 'Delicious Post'}, ...}
     }
 ]
}
```

_NB: Most example "output", as is the case above, will be elided for clarity and/or for the sake of omitting data irrelevant to the examples._

We see that there is a 'Last Post on Delicious tagged...' Block which we can use to retrieve the Delicious posts we're interested in working with, but there are no available HipChat Blocks. So in the next section, we'll show an example of creating a custom Block (along with an associated Type) which we can then use to "talk" to the HipChat service from our Stack.

### Custom Blocks - creating the "HipChat - Send Message to Room" Block

In order to create a custom Block, the main thing we need to do is create it's "definition" structure. This structure must conform to the JSON Schema for a Block Type. The following example is such a structure that we will create as the definition of our custom Block which will send a message to a HipChat room:

```python
>>> hipchat_block_def = {
...     u'actions': [
...         {u'action': u'uri_post',
...          u'action_params': {
...              u'body': u'room_id=\x01\x01/input/room_id\x02\x02&from=Kragle&message=\x01\x01%URLENCODE_PLUS('\x01\x01/input/content\x02\x02')\x02\x02',
...              u'headers': {'content-type': 'application/x-www-form-urlencoded'},
...              u'response_type': u'json',
...              u'uri': u'https://api.hipchat.com/v1/rooms/message?auth_token=\x01\x01/input/auth_token\x02\x02&format=json'}}],
...     u'initial_state': {u'input': {}, u'output': {}},
...     u'io': {u'input': u'HipChat - New Message for Room', u'output': None},
...     u'state': {u'input': {}, u'output': {}}}
>>>
```

_For those who are interested in the schema for a Block, it is currently available here: [Block Type Schema](https://raw.githubusercontent.com/sandbender/json_schema/master/block_type.json)._

Our Block only has a single Action - it makes a POST request to the appropriate HipChat api url which will result in a message being sent to the appropriate room.

The parameters for our Action contain the uri to which the request will be made, the body of the request, as well as a custom HTTP header for Content-Type since we will be sending form-encoded data, and finally an indicator that we expect JSON data to be returned in the response.

Note that both the `body` and `uri` parameters contain 'replacement markers' - special strings delimited with ASCII `0x01` (and `0x02`) characters. These replacement marker will be substituted with the corresponding data from the input structure which is fed to every Action.

_For details on the format of the structure used as the input for this conversion, see the [Advanced Topics - Creating Block Types (The Action input structure)](./Advanced_Topics/Creating_Block_Types.md#action_input_structure) section in the documentation._

_For details on how the conversion mechanism works, refer to the [API-specific Topics - Conversion Specifiers](./API-specific_Topics/Conversion_Specifiers.md) section of this documentation._

We must also specify "state" parameters in our Block definition; in this case, since our Block requires no state data to be persisted in between executions, both `state` and `initial_state` in the definition are left at the "default" values. (These are required elements of a Block definition, so we must include them explicitly, however by leaving both the `input` and `output` properties as empty objects (`{}`), for both `state` and `initial_state`, we signify that our Block requires no state data to be saved.)

_For further details on how to use State within your custom Block definitions, see the [Advanced Topics - Creating Block types (State)](./Advanced_Topics/Creating_Block_Types.md#state) section of this documentation._

Last but not least, we must specify what Types our new Block expects as input and output. We specify `None` (JSON `null`) as the output type since we can ignore the output in this case, and 'HipChat - New Message for Room' as the input. This input Type is one which does not yet exist, that we will create in the next section.

Now that we have our Block definition ready, we setup the payload for our 'create Block' request, which contains a name for our new Block, and the definition:

```python
>>> new_block_payload = {'name': 'HipChat - Send Message to Room', 'definition': hipchat_block_def}
```

Before we create this Block let's validate it first to make sure there are no glaring errors.

Conveniently, the "validate" endpoint for Blocks takes exactly the same parameters as the 'create-Block' endpoint, so we can use the same payload to first validate our payload:

```python
>>> new_block_validation = requests.post('https://kragle.io/api/v1/validate/blocktypes', headers=body_headers, cookies=cookies, data=json.dumps(new_block_payload))
>>> new_block_validation.json()
{u'validated': True}
```

Awesome, looks good! Let's create it for real:

```python
>>> new_block_result = requests.post('https://kragle.io/api/v1/blocktypes', headers=body_headers, cookies=cookies, data=json.dumps(new_block_payload))
>>> new_block_result.json()
{u'id': 4}
```

Our new "Send message to HipChat room" Block has now been created, with a Block id of 4.

#### Create a new Type - "HipChat - New Message for Room"

Now that we have our new HipChat Block, we still need to create something else before we can use this in a Stack: for the input Type to the Block, we've specified `HipChat - New Message for Room`, which doesn't exist yet since we just made that up. In order for this Block to work, we'll need to create that Type.

By specifying the input Type that a Block uses, we will know how to convert other data Types into a structure usable as input for this Block, which Kragle will validate to ensure that your Block receives the data it expects. Conversely, by specifying the output Type we will have a known structure to use as input for a conversion when/if this Block gets stacked on another which expects a different Type as it's own input.

Type definitions, unlike Block definitions, are JSON Schema and are used directly to validate a structure as an instance of the given Type.

Here is the Type definition (schema) for our new "HipChat - New Message for Room" Type:

```python
>>> hipchat_message_to_room_type_definition = {
...     '$schema': 'http://json-schema.org/draft-04/schema#',
...     'title': 'Kragle-data_type__hipchat_message_to_room-schema-v1.0',
...     'type': 'object',
...     'properties': {
...         'room_id': {'type': 'string', 'minLength': 1},
...         'content': {'type': 'string'},
...         'auth_token': {'type': 'string', 'minLength': 1}
...     },
...     'required': ['room_id', 'content', 'auth_token']
... }
>>> 
```

Our new Type says that a validating object must have:

1. a property named 'room_id' which is a string, at least one character in length.
1. a property named 'auth_token', which is also a min-length-1 string.
1. a property named 'content', which is a string of any length.

You'll note that the structure we've defined here ensures that an example of this Type will contain the information we've referred to in the replacement markers of our Block definition. For example, in our Block definition for "HipChat - Send Message to Room" we specified 'https://api.hipchat.com/v1/rooms/message?auth_token=\x01\x01/input/auth_token\x02\x02&format=json' as the uri to POST to. The substring `\x01\x01/input/auth_token\x02\x02` will be replaced with the `auth_token` element of the `input` object to this Block. The new Type we just defined contains `auth_token` as a required element, and so by specifying "HipChat - New Message for Room" as the expected input type to our Block, and then creating that Type with `auth_token` as a required element, we've ensured that when our Block is executed, the input it receives will contain the data necessary to convert this uri "template" into a usable value with the appropriate auth token.

Once again, let's create the Type payload and validate it first to make sure it's sane:

```python
>>> new_type_payload = {'name': 'HipChat - New Message for Room', 'definition': hipchat_message_to_room_type_definition}
>>> 
>>> new_type_validation = requests.post('https://kragle.io/api/v1/validate/types', headers=body_headers, cookies=cookies, data=json.dumps(new_type_payload))
>>> new_type_validation.json()
{u'validated': True}
```

Great! Now let's create the new type:

```python
>>> new_type_result = requests.post('https://kragle.io/api/v1/types', headers=post_headers, cookies=cookies, data=json.dumps(new_type_payload))
>>> new_type_result.json()
{u'id': 14}
```

And now our new Block and it's associated Type are ready for us to use!

#### <a name='stack_creation'></a>Creating a Stack

Once we know what we want our Stack to do, and we've identified the available Blocks we can stack together to make that happen, we can use the "POST /blockstacks" endpoint to create our Stack.

In the case of our Delicious -> HipChat example, we will use the "Last Post on Delicious tagged..." Block, and will stack it on top of the "HipChat - Send Message to Room" Block.

The main thing we need to know, aside from having chosen the Blocks we wish to use in our Stack, is the Type that each of our Blocks expects as input so that when stacking them together we can provide an appropriate [Conversion Specifier](./API-specific_Topics/Conversion_Specifiers.md) to provide the required input.

The definition (schema) for a 'Delicious Tag Search', which is the input type to our Delicious block, looks like this (as JSON):

```json
{
    "$schema": "http://json-schema.org/draft-04/schema#",
    "title": "Kragle-data_type__deicious_tag_search-schema-v1.0",
    "type": "object",
    "properties": {
        "tag": {
            "type": "string",
            "title": "Find recent Delicious posts tagged with this",
            "minLength": 1
        }
    },
    "required": ["tag"]
}
```

So we'll need to provide that block with a conversion specifier that results in an object with a single property, 'tag', whose value is the tag we want to search for in our recent Delicious posts.

The Delicious block outputs a "Delicious Post" structure, whose Type definition (schema) looks like this:

```json
{
    "$schema": "http://json-schema.org/draft-04/schema#",
    "title": "Kragle-data_type__delicious_post-schema-v1.0",
    "type": "object",
    "properties": {
        "description": {"type": "string", "minLength": 1},
        "url": {"type": "string"},
        "tags": {"type": "array", "items": {"type": "string"}},
        "hash": {"type": "string", "minLength": 1}
    },
    "required": ["description", "url", "tags", "hash"]
}
```

This is the format of the structure which we'll need to convert into the **input** Type that our HipChat Block expects. Our HipChat block expects a "HipChat - New Message for Room" structure as input, whose schema looks like this:

```json
{
    "$schema": "http://json-schema.org/draft-04/schema#",
    "title": "Kragle-data_type__hipchat_message_to_room-schema-v1.0",
    "type": "object",
    "properties": {
        "room_id": {"type": "string", "minLength": 1},
        "content": {"type": "string"},
        "auth_token": {"type": "string", "minLength": 1}
    },
    "required": ["room_id", "content", "auth_token"]
}
```

To put that all in context, here's how we will use our knowledge of the format of each of those Type structures to provide appropriate conversion specifiers when buiding our Stack. Here is the payload for the create-Stack request we're about to make:

```python
new_stack_payload = {
    'name': 'Send Delicious links to my coworkers on HipChat',
    'blocks': [
        ['Last Post on Delicious tagged...', {'tag': 'coworkers'}],
        ['HipChat - Send Message to Room', {'room_id': '1234567', 'auth_token': 'ABCDEF0987654321', 'content': 'New Link! \x01\x01/url\x02\x02'}]
    ]
}
```

Our stack is simply a list of Blocks which are executed in order, along with Conversion Specifiers used to transform their input into the appropriate Type that the Block expects. See the [API-specific Topics - Conversion Specifiers](./API-specific_Topics/Conversion_Specifiers.md) section of these docs for more information on Conversions.

For our 'Last Post on Delicious tagged...' Block we specify a structure containing no replacement markers for conversion - this will be passed as-is, and is a valid 'Delicious Tag Search' which is the type that this Block expects.

For our 'HipChat - Send Message to Room' Block, we pass a structure which contains the `room_id` and `auth_token` we need, as well as the `content` of the message, which contains a replacement marker. This replacement marker's contents, `/url`, refer to the "path" of a piece of data in the input it receives. Since the Delicious Block is stacked on top of it, the **output** Type of that Delicious Block is what will be used as input for this conversion. The output of our Delicious Block is a "Delicious Post" structure, which among other things contains a `url` property. The value of `url` from that "Delicious Post" output by the Delicious Block will be used as the value to replace in our conversion structure for the HipChat Block, and in this way we use the data output by the first block as input for the second.

You can always use the [Conversion](./API_Reference/Conversion.md) api endpoint to test your conversion structures beforehand.

**It is important to note here that you should specify "runtime configuration" data for a Block via the Conversion Specifiers for that Block within a Stack.** These Conversion Specifiers in a Stack's list of Blocks are specific to the instance of the Block within _that particular stack_. Conversely, anything "hard-coded" into the definition of a Block will be present in **every** instance of that Block.

Now that we have everything setup, let's create our Stack:

```python
>>> new_stack_response = requests.post('https://kragle.io/api/v1/blockstacks', headers=body_headers, cookies=cookies, data=json.dumps(new_stack_payload))
>>> new_stack_response.json()
{u'id': 2}
```

Excellent - we just created our first Stack!

#### Testing our Stack

Now that the Stack is created, it hasn't yet been activated - since this is a 'regular' (vs. webhook) style stack, it will ultimately be run periodically by the Kragle system once enabled via a Job; a Job takes a Stack id and a Schedule id and creates a recurring job which executes the Stack in question every so often, according to the configuration of the Schedule.

For now though, we just want to manually run the Stack once to see if it works (after checking that we've saved a post on Delicious today tagged with "coworkers").

We can do this via the [Webhooks](./API_Reference/Webhooks.md) "exec" endpoint like so:

```python
>>> requests.get('https://kragle.io/api/v1/blockstacks/exec/2', cookies=cookies).json()
{u'success': [5, 6]}
```

The `success` element of the response indicates that our Stack was executed without error, and the value is the sequential list of Block instances which were executed as part of this Stack.

We now go to our HipChat window/app, and see that a new message with the appropriate content was indeed posted to our room.

Success!

### Where to go from here...

Once you've tested your new Stack successfully, you can use the Schedule endpoints to list the available Schedules under which your Stack can be setup to run (every 15 mins, etc).

The Jobs endpoints can then be used to create a Job, which ties a Stack to a specific Schedule and (optionally) activates it to be run according to the schedule in question.

Job and Schedule operations will be left as an excercise for the reader.

We also encourage you to explore other areas of the API and documentation to see what's possible and browse all the available functionality. For example, there are a (constantly growing) number of different **functions** available for use in Conversion Specifiers, and different Actions you can use when creating your own Blocks as well.

If you skipped over the "Creating Custom Blocks and Types" sections of this page, now would be a good time to revisit those areas if that interests you - and we hope it will!

You can always use the support features on the site to send us questions, feature requests, and report bugs if you happen to find any!

##### [Previous Topic: Faults and Error Codes](./API_Reference/Faults.md)

### [Back to Table of Contents](./README.md)
