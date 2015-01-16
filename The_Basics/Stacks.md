# Kragle.io Documentation

### [Table of Contents](../README.md)

##### [Previous Topic: How it works](../How_it_works.md)

##### [Next Topic: The Basics - Blocks](./Blocks.md)

## Stacks

### More Details about Stacks...

##### Using information from previous Blocks in the Stack

When configuring a Block that is stacked on the 'end' of another one, the information that the other one produces will be available
for you to use when configuring the current Block. For example, if you stack the *"Retweet"* block on the end of *"New Tweet matching Hashtag..."* block, configuring *"Retweet"* will require you to provide the id of the tweet you want to retweet from your account. The *"New Tweet matching Hashtag..."* block provides (among a few other things) the id of the tweet it found, and the system will give you a placeholder
that you can select/use when filling out the 'id' configuration for *"Retweet"*.

##### Blocks are run in the Order in which they're stacked

It is **important** to note here that blocks are run in the order you stack them. The first block is run first, and it sends it's information to the second, which is run next and sends it's information to the third, and so on. Block A -> Block B -> Block C is a different stack and will work/be configured/etc differently from a Stack made up of Block C -> Block A -> Block B.

##### Stacks can have multiple Jobs, but it's Jobs must be different

You can attach more than one Job to a stack (ie: "every 5 mins" as well as "Tuesdays at 8pm", etc), but each of the Jobs attached
to a Stack must have a different schedule. You can not add a Job to a stack using an "every 5 mins" schedule if the Stack already
has a Job that runs every 5 mins.

##### [Next Topic: The Basics - Blocks](./Blocks.md)

##### [Previous Topic: How it works](../How_it_works.md)

### [Back to Table of Contents](../README.md)

