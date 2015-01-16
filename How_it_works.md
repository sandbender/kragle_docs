# Kragle.io Documentation

### [Table of Contents](./README.md)

##### [Previous Topic: What is Kragle?](./What_is_Kragle.md) [Next Topic: The Basics - Stacks](./The_Basics/Stacks.md)

## How it works

#### First, a tiny bit of terminology...

- A single action that can be performed by Kragle is called a *Block*
- A set of *Blocks* that have been glued together is called a *Stack*
- A *Stack* can have one or more (or zero) *Jobs* associated with it, which indicate when/how often the *Stack* is to be run.

### Creating your Stacks

When creating a Stack, you first choose a Block to start with from the list of available pre-defined and community-generated Blocks.

Each Block will usually have certain information that you need to fill out in order to configure it. For example, if you
select the 'New Tweet matching Hashtag...' Block, you will need to fill in the #hashtag you want to search for.

Once you have your first Block setup, you select another Block to stack onto the end of the first, and configure it the same way.

When configuring a Block that is stacked on the 'end' of another one, the information that the other one produces will be available
for you to use when configuring the current Block. For example, if you stack the 'Retweet' block on the end of 'New Tweet matching Hashtag...' block, configuring 'Retweet' will require you to provide the id of the tweet you want to retweet from your account. The 'New Tweet
matching Hashtag...' block provides (among a few other things) the id of the tweet it found, and the system will give you a placeholder
that you can select/use when filling out the 'id' configuration for 'Retweet'.

You then proceed with adding more blocks to the stack, or stop there if/when you are finished.

*It is **important** to note here that blocks are run in the order you stack them. The first block is run first, and it sends it's information to the second, which is run next and sends it's information to the third, and so on. Block A -> Block B -> Block C is a different stack and will work/be configured/etc differently from a Stack made up of Block C -> Block A -> Block B.*

##### [Previous Topic: What is Kragle?](./What_is_Kragle.md) [Next Topic: The Basics - Stacks](./The_Basics/Stacks.md)

