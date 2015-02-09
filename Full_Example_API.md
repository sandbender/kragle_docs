# Kragle.io Documentation

### [Table of Contents](./README.md)

##### [Previous Topic: Faults and Error Codes](./API_Reference/Faults.md)

## A Full Example/Walkthrough, via the API

**NB:** All the following code is written with Python 2.7 and utilizes the 'requests' and 'json' libraries. However all the examples should be easily adaptable to any other language with support for basic HTTP requests and json encoding/decoding.

In this example, we'll create a connection between a Delicious.com account and a Hipchat room.

We want to have Kragle post a message to the Hipchat room, which contains some of our coworkers, whenever we post a new url to Delicious that's tagged with "coworkers".

To start off with, we'll need to setup some basics and authenticate to get a session auth cookie from Kragle:

```python
>>> import requests
>>> import json
>>> import pprint
>>>
>>> post_headers = {'content-type': 'application.json'}
>>>
>>> login_payload = {'username': 'SandbenderCa', 'password': 'itsapassword'}
>>>
>>> response = requests.post('https://kragle.io/api/v1/authenticate', headers=post_headers, data=json.dumps(login_payload))
>>>
>>> cookies = response.cookies.get_dict('kragle.io')
>>> cookies
{'auth_tkt': '"11111111111111122222222222222222222223333333333333334444444444444445555555555555555666666666666666667777777777777788888888888888999990000!userid_type:int"'}
```

We setup a `post_headers` dict to use when making post requests (Content-Type), and make a request to the authentication endpoint to get our cookie. We also import the libraries we'll be using: requests, json and pprint.

#### Get a list of available Blocks to see how we can connect to Delicious...

Next, we make a call to the `/blocktypes` endpoint to see what's available:

```python
>>> pprint.pprint(requests.get('https://kragle.io/api/v1/blocktypes?show_resolved=1', cookies=cookies).json())
{u'blocktypes': [{u'definition': u'https://raw.githubusercontent.com/sandbender/json_schema/master/block__delicious_tag_results_last.json',
                  u'id': 2,
                  u'is_approved': False,
                  u'is_remote': True,
                  u'name': u'Last Post on Delicious tagged...',
                  u'needs_approval': False,
                  u'owner': 3,
                  u'resolved': {u'actions': [{u'action': u'uri_get',
                                              u'action_params': {u'auth_required': True,
                                                                 u'auth_type': u'strategy',
                                                                 u'response_type': u'xml',
                                                                 u'uri': u'https://api.del.icio.us/v1/posts/get?tag=\x01\x01/input/tag\x02\x02&meta=yes'}},
                                             {u'action': u'convert',
                                              u'action_params': {u'conversion': [u'\x01',
                                                                                 u'\x01/input/_children',
                                                                                 {u'description': u'\x01/description',
                                                                                  u'hash': u'\x01/hash',
                                                                                  u'tags': u"\x01%SPLIT_ON_CHAR('\x01\x01/tag\x02\x02', ' ')",
                                                                                  u'url': u'\x01/href'}]}},
                                             {u'action': u'exclude_in',
                                              u'action_params': {u'element_path': u'/hash',
                                                                 u'exclusions': u'\x01/output_state/hashes'}},
                                             {u'action': u'take_last',
                                              u'action_params': {}}],
                                u'initial_state': {u'input': {},
                                                   u'output': {u'hashes': []}},
                                u'io': {u'input': u'Delicious Tag Search',
                                        u'output': u'Delicious Post'},
                                u'state': {u'input': {},
                                           u'output': {u'hashes': [u'\x01/hash']}}}}],
 u'count': 1,
 u'page': 1,
 u'pagesize': 10}
```

Ah, exactly what we need, a Block which will return the last Delicious post (for my account) with a given Tag!

#### Create the needed HipChat Block type so we can send messages to HipChat rooms via Kragle...

Unfortunately, we see no HipChat Blocks ready-made. Luckily, this is not an issue - we can simply create a "HipChat - Send Message to Room" block, like so:

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
