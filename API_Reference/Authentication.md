# Kragle.io Documentation

### [Table of Contents](../README.md)

##### [Previous Topic: API Reference](../API_Reference.md)

##### [Next Topic: Blocks](./Blocks.md)

## API Reference - Authentication endpoints


### POST /authenticate

_(This endpoint does **not** require authentication)_

Authenticate against the Kragle api. A successful response will be: `{'code': 0, 'user_id': <your user id>}` and will have a 200 HTTP response code as usual.

*NB*: The response headers are relevant - you will need the cookie returned by a successful authenticate call whose value is a json string containing `auth_tkt`. You may or may not need to do this manually, depending on the HTTP library/code you're using.

**Required parameters**:
  - `password` - string - Your password.
  - `email` - string - The email address associated with your account.
    - Either email OR username are required, not both. If both are provided, username is used/takes precedence.
  - `username` - string - Your Kragle username (or Twitter screen name if your account was created via "Sign in with Twitter", etc)

#### Notes regarding authentication for Block actions

If you are creating a Stack through the API which uses a block that needs OAuth credentials for a request, you'll need to grant Kragle authorization to access your account before the Stack will work. See [The Basics - Blocks](../The_Basics/Blocks.md)

This is accomplished with the oauth url for the domain in question. For example, if you are using a Block which makes a request to the url http://api.del.icio.us/v1/posts/get , and that url requires oauth credentials (due to the auth_type being "strategy" in the Block definition), you will need to login to Kragle and visit https://kragle.io/oauth/redirect/api.del.icio.us, which will perform the appropriate redirect and prompt you on the target site to grant access to Kragle. Once you grant access and are redirected back to Kragle, the appropriate credentials will be available for use by your Kragle Stacks.

The general format of the authorization url for a Block which uses the 'strategy' auth type (ie: some form of OAuth) is:

```
https://kragle.io/oauth/redirect/<domain>
```

... where `domain` is the hostname (domain name) of the url for which your Kragle Stacks require access.

##### [Next Topic: Blocks](./Blocks.md)

##### [Previous Topic: API Reference](../API_Reference.md)

### [Back to Table of Contents](../README.md)

