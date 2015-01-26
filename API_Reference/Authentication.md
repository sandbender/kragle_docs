# Kragle.io Documentation

### [Table of Contents](../README.md)

##### [Previous Topic: API Reference](../API_Reference.md)

##### [Next Topic: Blocks](./Blocks.md)

## API Reference - Authentication endpoints

### POST /authenticate

Authenticate against the Kragle api. A successful response will be: `{'code': 0, 'user_id': <your user id>}` and will have a 200 HTTP response code as usual.

Required parameters:
`password` - string - Your password.
`email` - string - The email address associated with your account.
  - Either email OR username are required, not both. If both are provided, username is used/takes precedence.
`username` - string - Your Kragle username (or Twitter screen name if your account was created via "Sign in with Twitter", etc)

##### [Next Topic: Blocks](./Blocks.md)

##### [Previous Topic: API Reference](../API_Reference.md)

### [Back to Table of Contents](../README.md)

