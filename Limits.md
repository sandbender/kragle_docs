# Kragle.io Documentation

### [Table of Contents](./README.md)

##### [Previous Topic: The Basics](../../tree/master/The_Basics)

##### [Next Topic: Limits](./Available_Blocks)

## Limits

There are various limits imposed on the resources that your Stacks will require, which are explained here.

#### Maximum # of Block executions per month

This limit is dictated by the type of account you have. Free accounts are limited to 150,000 individual Block executions per month.

This translates into roughly 200 Block executions per hour, or slightly more than 3 Block executions every minute, 24x7.

#### Size limits

If you are creating your own Block Types or Types, their definitions are limited to a maximum of 64 kilobytes in size when represented as JSON strings.

There is no limit on inbound bandwidth, however individual Blocks are prevented from loading HTTP responses greater than 1 megabyte in size - Stacks or Blocks which request HTTP resources that result in a response larger than this will be disabled.

Outbound bandwidth is limited to 24 kilobytes per block, and is tracked on a monthly basis. If you have 10 active blocks whose Job(s) use up your entire allowance of Block executions per month, but only 1 of those blocks is sending data in an HTTP POST (request headers and other network overhead do not count against your outbound bandwidth limites), that 1 block will be able to send up to 240k of data each time it executes. Outbound data (ie: POST payloads, responses to 'webhook' style Stacks served to clients, etc) also has a hard limit of 1 megabyte per request - Stacks/Blocks attempting to send more than this will be disabled.

The 'One Megabyte' limit also applies to any State saved by your Stacks; the total size of any data saved this way may not exceed 1 megabyte, or the Stack will be disabled.

#### Number of (active) Stacks, Number of Blocks per Stack, etc.

There are no limits on the number of active, inactive or total Blocks used by your account, nor is there any limit on the number of Stacks you have defined or the number of Blocks per Stack, etc. The only limit on Stacks and Blocks is the monthly # of Block executions per account.

##### [Next Topic: Limits](./Available_Blocks)

##### [Previous Topic: The Basics](../../tree/master/The_Basics)

### [Back to Table of Contents](./README.md)
