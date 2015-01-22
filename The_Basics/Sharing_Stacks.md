# Kragle.io Documentation

### [Table of Contents](../README.md)

##### [Previous Topic: Jobs](./Jobs.md)

##### [Next Topic: Limits](../Limits.md)

## Sharing Stacks

Being able to share your Stack "blueprints" with the community is one of the most important feature of Kragle.

When you think of a creative use for Kragle, or figure out a useful way to connect two services, or even just put together a Stack
which does something simple but practical that a lot of other people might like to do too, you can share your Stack publicly on
Kragle so that others can copy it and use it as well (or make their own customizations, etc). The system is designed to be as open
and collaborative as possible - we want to see what you'll come up with, and we want you to surprise us with the cool things you're
doing with Kragle and share that with the community!

### Privacy/Personal Info/etc - what happens when I share a Stack?

All your personal and user-specific information is saved separately from things that are shared (including Stacks) and will not be
exposed to other users when you share your creations. If a stack is built with a block that needs access information for a certain
site (example: a Block which posts to Twitter on your user's behalf), the access credentials (tokens, or username/password, etc) is
automatically determined based on the current user or owner of a **specific copy** of a Stack. When **you** use that stack, Kragle
will use access credentials tied to *your* account. When someone **else** uses the same stack, the system will automatically use
**their** credentials when running that block, etc.

### What about Block configuration - I don't want other users seeing my hashtag search for Topic X when they copy my Stack?

Not to worry... all configuration information for Blocks in a Stack is tied to that specific **copy** of the Stack. When you share a
stack, you are **not** sharing the specific configuration you have setup, just the **structure** of the stack itself. When another
user copies a stack you've shared, they'll need to fill out their own specific configuration for that stack before they can use it,
since that will be empty when the first copy it and will **not** be copied from yours.

As an example, if I have a Stack which searches Twitter for a specific #hashtag, and then re-tweets any results to my account, I
could share that Stack so other people could do the same thing. When someone else copies that Stack, the copy won't be pre-configured
with my search for a specific hashtag - they'll have to fill out the search with their own hashtag-of-interest since it will be
blank initially. It's only the **structure** of the Stack which is shared/copied/etc, not the actual detail of how I have my own
copy "filled out".

##### [Next Topic: Limits](../Limits.md)

##### [Previous Topic: Jobs](./Jobs.md)

### [Back to Table of Contents](../README.md)

