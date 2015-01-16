# Kragle.io Documentation

### [Table of Contents](../README.md)

##### [Previous Topic: The Basics - Stacks](./Stacks.md)

##### [Next Topic: The Basics - Jobs](./Jobs.md)

## Blocks

### Access/Authentication - How Kragle talks to your apps

Each type of Block usually performs a single action. If that action requires no contact with other services/sites/apps, you will not
be required to do anything other than fill out it's basic configuration when using it.

However, in many cases a Block will require some kind of restricted access to the service/site in question in order to perform it's
duty. For example, posting a new Tweet to your account on twitter.com will require Kragle to have been granted access to your account.

In this situation, the first time you attempt to use a Block needing that specific type of access, you will be prompted to grant the
access in question to Kragle. This usually happens via secure redirect to the site/service in question, where you can authorize the
access, and the site will then redirect you back to Kragle.

This works without needing to share any of your account details (ie: password) with Kragle - the site provides us with a 'token' that
we subsequently use when performing actions that require this access to your account. The token is how the site in question knows
that we've been authorized to use your account. At any time, you can return to the site and revoke this access.

This authorization flow (Site A -> redirect to Site B -> grant access to Site A -> redirect to Site A) is likely familiar to you
already, and is essentially the same mechanism used by mobile apps (example: a Twitter client for Android) when you grant them access
to work with your account.

##### [Next Topic: The Basics - Jobs](./Jobs.md)

##### [Previous Topic: The Basics - Stacks](./Stacks.md)

### [Back to Table of Contents](../README.md)

