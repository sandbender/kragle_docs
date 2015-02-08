# Kragle.io Documentation

### [Table of Contents](./README.md)

##### [Previous Topic: Faults and Error Codes](./API_Reference/Faults.md)

## A Full Example/Walkthrough, via the API

**NB:** All the following code is written with Python 2.7 and utilizes the 'requests' and 'json' libraries. However all the examples should be easily adaptable to any other language with support for basic HTTP requests and json encoding/decoding.

In this example, we'll create a connection between a Delicious.com account and a Hipchat room.

We want to have Kragle post a message to the Hipchat room, which contains some of our coworkers, whenever we post a new url to Delicious that's tagged with "coworkers".

To start off with, we'll need to setup some basics and authenticate to get a session auth cookie from Kragle:

```
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

```
>>> pprint.pprint(requests.get('https://kragle.io/api/v1/blocktypes?show_resolved=1', cookies=cookies).json())
{u'blocktypes': [{u'definition': u'https://raw.githubusercontent.com/sandbender/json_schema/master/block__delicious_tag_results_last.json',
                  u'id': 2,
                  u'is_approved': False,
                  u'is_remote': True,
                  u'name': u'Delicious Posts tagged...',
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
                                u'io': {u'input': u'delicious_tag_search',
                                        u'output': u'delicious_post'},
                                u'state': {u'input': {},
                                           u'output': {u'hashes': [u'\x01/hash']}}}}],
 u'count': 1,
 u'page': 1,
 u'pagesize': 10}
```

Ah, exactly what we need, a Block which will return the last Delicious post (for my account) with a given Tag!

Unfortunately, we see no HipChat Blocks ready-made. Luckily, this is not an issue - we can simply create a "HipChat - Send Message to Room" block, like so:

```
>>> hipchat_block_def = {
...     u'actions': [
...         {u'action': u'uri_post',
...          u'action_params': {
...              u'body': u'room_id=\x01\x01/input/room_id\x02\x02&from=Delicious+Kragle&message=\x01\x01%URLENCODE_PLUS(\x01\x01/input/content\x02\x02)\x02\x02',
...              u'headers': {'content-type': 'application/x-www-form-urlencoded'},
...              u'response_type': u'json',
...              u'uri': u'https://api.hipchat.com/v1/rooms/message?auth_token=\x01\x01/input/auth_token\x02\x02&format=json'}}],
...     u'initial_state': {u'input': {}, u'output': {}},
...     u'io': {u'input': u'hipchat_message_to_room', u'output': None},
...     u'state': {u'input': {}, u'output': {}}}
>>>
```

We've specified that our block should have one action, which makes the POST request to the appropriate HipChat api endpoint. We've also specified a body for the request which is a mix of static content and data which will come from the input to this block. You'll also notice that there is an 'auth_token' parameter to the HipChat url (for their auth), whose value also comes from what will be the input to this block, and we specified an explicit Content-Type header since we're using a 'form style' url-encoded values.

Now we'll add this to a payload object for a request to create a new Block:

```
>>> new_block_payload = {'name': 'HipChat - Send Message to Room', 'definition': hipchat_block_def}
```

Next, before we create this Block let's validate it first to make sure there are no glaring errors. Conveniently, the validate endpoint takes exactly the same parameters as the 'live' create-Block endpoint, so we can use the same payload to first validate...

```
>>> new_block_validation = requests.post('https://kragle.io/api/v1/validate/blocktypes', headers=post_headers, cookies=cookies, data=json.dumps(new_block_payload))
>>> new_block_validation.json()
{u'validated': True}
```

Awesome, looks good! Let's create it for real:

```
>>> new_block_result = requests.post('https://kragle.io/api/v1/blocktypes', headers=post_headers, cookies=cookies, data=json.dumps(new_block_payload))
>>> new_block_result.json()
{u'id': 3}
```

Our new "Send message to HipChat room" Block has now been created, with a Block id of 3.

Now that we have our block, we still need to create something else before we can use this in a Stack: for the input Type to the Block, we've specified `hipchat_message_to_room`, which doesn't exist yet since we just made that up. In order for this Block to work, we'll need to create that Type:

```
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

```
>>> new_type_payload = {'name': 'Hipchat - New Message for Room', 'definition': hipchat_message_to_room_type_def_obj}
>>> 
>>> new_type_validation = requests.post('https://kragle.io/api/v1/validate/types', headers=post_headers, cookies=cookies, data=json.dumps(new_type_payload))
>>> new_type_validation.json()
{u'validated': True}
```

Great! Now let's create the new type:

```
>>> new_type_result = requests.post('https://kragle.io/api/v1/types', headers=post_headers, cookies=cookies, data=json.dumps(new_type_payload))
>>> new_type_result.json()
{u'id': 13}
```

And now our new Block and it's associated Type are ready for us to use!

##### [Previous Topic: Faults and Error Codes](./API_Reference/Faults.md)

### [Back to Table of Contents](./README.md)
