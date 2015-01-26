# Kragle.io Documentation

### [Table of Contents](../README.md)

##### [Previous Topic: Actions available for Block types](./Actions_for_Block_Types.md)

##### [Next Topic: Creating your own Types](./Creating_Types.md)

## Types are really just JSON Schema

When we refer to the notion of 'Types' in Kragle, ie: the Types of data specified as input and output to a Block, those Types are essentially just arbitrary data structures, and can be anything including simple types of data (string, integers, etc) or complex structures (objects with nested arrays, etc).

A Type is defined using a JSON Schema. Any data structure which validates against the schema for a given Type (ie: the 'definition' of that Type) is treated as a valid instance of that Type.

### The Type schema

There is no specific schema for a Type schema itself - any JSON object which is a valid JSON Schema can be used as a Type definition.

#### The GUI Type schema

Contrary to the preceding rule stating that 'any valid JSON Schema can be used as a Type', for a Type to be usable within the Kragle (web)app, it must conform to a simple schema known as the 'GUI Type' schema.

Specifically, this schema requires that an object validating as a 'GUI Type' schema must:
1. be an object
1. not have any properties which themselves are objects or arrays

**The GUI Type schema is available via the [Links](../Links.md) section of this documentation.**

The reason for this is to maintain a simple, user-friendly interface to the system. Allowing nested objects or arrays in Types being used as input or output from Blocks in the (web)app would dramatically increase the complexity of the system from a web- or app-user's point of view. As such, we've made the decision to restrict Types usable in the (web)app to this form of 'simple' input/output structure.

#### Implications of the GUI Type for custom Blocks

Where the GUI Type restrictions come into play is when you are creating custom Blocks which you intend to share, ie: make available for other users to incorporate into their own Stacks. If you are creating such a custom Block (and we hope you will!), be aware of this restriction: **any Block which specifies an input or output Type that does NOT validate against the GUI Type schema will NOT be visible/available to users of the (web)app only.**

**NB:** This does **not** affect API usage: you are still free to use input/output Types which are as complex as you wish when interacting with the service via the API. Furthermore, you may also make non-GUI Types available (shared) for other users. In this case, users will still not see these Types (or Blocks which use them) in the (web)app, however they **will** still be able to see/use these Types when interacting with the service via the API themselves.

### The Block schema

As mentioned elsewhere in this documentation, when creating custom Blocks the 'definition' must conform to a specific structure. This structure is also defined and validated by a JSON Schema: the Block Type schema.

**The Block Type schema is available in the [Links](../Links.md) section of this documentation.**

##### [Next Topic: Creating your own Types](./Creating_Types.md)

##### [Previous Topic: Actions available for Block types](./Actions_for_Block_Types.md)

### [Back to Table of Contents](../README.md)

