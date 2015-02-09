# Kragle.io Documentation

### [Table of Contents](./README.md)

##### [Previous Topic: Faults and Error Codes](./API_Reference/Faults.md)

## A Full Example/Walkthrough, via the API

**NB:** All the following code is written with Python 2.7 and utilizes the 'requests' and 'json' libraries. However all the examples should be easily adaptable to any other language with support for basic HTTP requests and json encoding/decoding.

In this example, we'll create a connection between a Delicious.com account and a Hipchat room.

We want to have Kragle post a message to the Hipchat room, which contains some of our coworkers, whenever we post a new url to Delicious that's tagged with "coworkers".

### Creating Stacks vs. Custom Blocks and Types

There are two parts to this walkthrough - the first involves creating custom Blocks and associated Types, when the action you want does not already exist as a Block in Kragle. The second part involves using Blocks (whether pre-existing or your own freshly created custom Blocks) to make a Stack that does what we want.

If you are you only interested in creating Stacks from predefined Blocks, you can skip down to the second section titled [Creating a Stack](#stack_creation). Otherwise, read on for an introduction to creating your own Kragle functionality with custom Blocks and Types.

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

Last but not least, we must specify what Types our new Block expects as input and output. 


We've specified that our block should have one action, which makes the POST request to the appropriate HipChat api endpoint. We've also specified a body for the request which is a mix of static content and data which will come from the input to this block. You'll also notice that there is an 'auth_token' parameter to the HipChat url (for their auth), whose value also comes from what will be the input to this block, and we specified an explicit Content-Type header since we're using a 'form style' url-encoded values.

Now we'll add this to a payload object for a request to create a new Block:

```python
>>> new_block_payload = {'name': 'HipChat - Send Message to Room', 'definition': hipchat_block_def}
```

Next, before we create this Block let's validate it first to make sure there are no glaring errors. Conveniently, the validate endpoint takes exactly the same parameters as the 'live' create-Block endpoint, so we can use the same payload to first validate...

```python
>>> new_block_validation = requests.post('https://kragle.io/api/v1/validate/blocktypes', headers=post_headers, cookies=cookies, data=json.dumps(new_block_payload))
>>> new_block_validation.json()
{u'validated': True}
```

Awesome, looks good! Let's create it for real:

```python
>>> new_block_result = requests.post('https://kragle.io/api/v1/blocktypes', headers=post_headers, cookies=cookies, data=json.dumps(new_block_payload))
>>> new_block_result.json()
{u'id': 3}
```

Our new "Send message to HipChat room" Block has now been created, with a Block id of 3.

#### Create a new Type which defines the input format for the Block we just created...

Now that we have our block, we still need to create something else before we can use this in a Stack: for the input Type to the Block, we've specified `HipChat - New Message for Room`, which doesn't exist yet since we just made that up. In order for this Block to work, we'll need to create that Type:

```python
>>> hipchat_message_to_room_type_definition = '''
... {
...     "$schema": "http://json-schema.org/draft-04/schema#",
...     "title": "Kragle-data_type__hipchat_message_to_room-schema-v1.0",
...     "type": "object",
...     "properties": {
...         "room_id": {"type": "string", "minLength": 1},
...         "content": {"type": "string"},
...         "auth_token": {"type": "string", "minLength": 1}
...     },
...     "required": ["room_id", "content", "auth_token"]
... }'''
>>> hipchat_message_to_room_type_def_obj = json.loads(hipchat_message_to_room_type_definition)
```

Our new type is a JSON Schema, like all Types, and says that an object which validates as this Type must have:
1. a property named 'room_id' which is a string, at least one character in length.
1. a property named 'auth_token', which is also a min-length-1 string.
1. a property named 'content', which is a string of any length.

Once again, let's create the create-Type payload and validate it first to make sure it's sane:

```python
>>> new_type_payload = {'name': 'HipChat - New Message for Room', 'definition': hipchat_message_to_room_type_def_obj}
>>> 
>>> new_type_validation = requests.post('https://kragle.io/api/v1/validate/types', headers=post_headers, cookies=cookies, data=json.dumps(new_type_payload))
>>> new_type_validation.json()
{u'validated': True}
```

Great! Now let's create the new type:

```python
>>> new_type_result = requests.post('https://kragle.io/api/v1/types', headers=post_headers, cookies=cookies, data=json.dumps(new_type_payload))
>>> new_type_result.json()
{u'id': 13}
```

And now our new Block and it's associated Type are ready for us to use!

#### Put it all together - make a Stack!

Now that we have all the pieces we need for our Stack, let's set it up. Here's the payload we'll use to create the stack:

```python
>>> new_stack_payload = {
...     'name': 'Send Delicious links to my coworkers on HipChat',
...     'blocks': [
...         ['Last Post on Delicious tagged...', {'tag': 'coworkers'}],
...         ['HipChat - Send Message to Room', {'room_id': '1234567', 'auth_token': 'ABCDEF0987654321', 'content': 'New Link! \x01\x01/url\x02\x02'}]
...     ]
... }
```

Our stack is simply a list of Blocks which are executed in order, along with Conversion Specifiers used to transform their input into the appropriate Type that the Block expects. See the [API-specific Topics - Conversion Specifiers](./API-specific_Topics/Conversion_Specifiers.md) section of these docs for more information on Conversions.

You can also use the [Conversion](./API_Reference/Conversion.md) api endpoint to test your conversion structures beforehand.

The Delicious Block expects a "Delicious Tag Search" as it's input, which is an object containing a single 'tag' property, so we specify this directly (we're searching for posts with the "coworkers" tag).

The "HipChat - Send Message to Room" Block that we previously created expects a "HipChat - New Message for Room" as input and so we use a Conversion Specifier that will result in an appropriate structure.

**Note that this is where we specify the `room_id` and `auth_token` for our request, and _not_ in the Block definition. Blocks are generic site-actions and you should not include any instance-specific information in them as public Blocks will be available for everyone, but somewhat useless if they're built using hard-coded defaults for things which need to be parameterized. Stacks, on the other hand, are instance-specific - anything in the Conversion Specifier for a Block within a Stack is _specific to that Stack_.** If our HipChat block is made public, others will be able to use it and specify their own "arguments" for the Blocks involved, in the form of their own Stack-specific Conversion Specifiers for their own Stacks.

Now let's create our stack:

```python
>>> new_stack_response = requests.post('https://kragle.io/api/v1/blockstacks', headers=post_headers, cookies=cookies, data=json.dumps(new_stack_payload))
>>> new_stack_response.json()
{u'id': 1}
```

Excellent - our first Stack!

#### Testing our Stack

Now that the Stack is created, it hasn't yet been activated - since this is a 'regular' (vs. webhook) style stack, it will ultimately be run periodically by the Kragle system once enabled via a Job; a Job takes a Stack id and a Schedule id and creates a recurring job which executes the Stack in question every so often, according to the configuration of the Schedule.

For now though, we just want to manually run the Stack once to see if it works (after checking that we've saved a post on Delicious today tagged with "coworkers").

We can do this via the [Webhooks](./API_Reference/Webhooks.md) endpoints like so:

```python

```

##### [Previous Topic: Faults and Error Codes](./API_Reference/Faults.md)

### [Back to Table of Contents](./README.md)
