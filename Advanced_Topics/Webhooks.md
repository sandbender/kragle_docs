# Kragle.io Documentation

### [Table of Contents](../README.md)

##### [Previous Topic: Creating your own Types](./Creating_Types.md)

##### [Next Topic: Links](../Links.md)

## Webhooks and Manually Triggered Stacks

### Webhooks

Stacks can be setup as 'webhooks', so that you can point other services which support this style of 'hook' at your Stack, instead of the usual setup whereby your Stack periodically executes and reaches out to external services.

In order to enable this method of 'activating' a Stack, you need to set the 'webhook' property of the Stack to one of the following allowed values: `public`, `token` or `user`.

When a webhook is set to `public`, there will be no authorization involved in calling it - **anyone with the url** for the webhook (Stack) will be able to make a request to that url in order to execute the Stack.

`token` webhooks require that the entity making a request to the url for the webhook provides a valid access token when making the request. An access token is separate from your user credentials - it is simply a unique, auto-generated string that grants access to the specific hook **only**. Tokens for a webhook are available as part of the Stack structure returned by the API methods for retrieving Stacks.

Tokens for a webhook can be refreshed (ie: a new one generated) via the 'token_refresh' api method for a hook. **NB**: Refreshing a token will overwrite the previous value - a hook can (currently) only have one token associated with it at any given time. This can be used as a convenient method of disabling any existing access to your webhook without removing it/disabling the token access entirely.

`user` webhooks require that the request to the webhook url can be authenticated as the user id who owns the stack (ie: private to the owner).

If the 'webhook' value of a Stack is `null`, the Stack is not available for use as a webhook.

#### Webhook HTTP methods

Webhooks are available via GET or POST HTTP request verbs (not both - if you need both, simply setup another matching Stack with an alternate method for the webhook). This is specified in the 'webhook_method' of the Stack.

If a Stack has POST as it's 'webhook_method', it will not be accessible via GET requests, and vice-versa.

#### Webhook URLs

The URL used to access a webhook follows this format (relative to the 'standard' API url prefix):

`/blockstacks/hook/<id>` where 'id' is the numeric id of the Stack being accessed as a webhook. The same hook url is used regardless of whether the webhook uses http GET or POST.

### Manually-Triggered Stacks

Similar to webhooks, it is possible for **the owner of a hook only** to manually trigger a Stack to execute.

Typically this is done via GET requests, however POSTs are also possible if you wish to setup webhook-style Stacks that accept POST request bodies, but do not have a recurring schedule and instead are triggered on demand only, etc.

The url for manually triggering one of your stacks is:

`/blockstacks/exec/<id>` where 'id' is the numeric id of the Stack being triggered. (This url is relative to the 'standard' API url prefix, typically something like `https://kragle.io/api/v1`.)

##### [Next Topic: Links](../Links.md)

##### [Previous Topic: Creating your own Types](./Creating_Types.md)

### [Back to Table of Contents](../README.md)

