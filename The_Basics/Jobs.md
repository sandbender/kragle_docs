# Kragle.io Documentation

### [Table of Contents](../README.md)

##### [Previous Topic: The Basics - Blocks](./Blocks.md)

##### [Next Topic: The Basics - Sharing Stacks](./Sharing_Stacks.md)

## Jobs

By default, Jobs are recurring and creating one simply involves choosing how often you want it to run (ie: every 5 minutes, every hour, etc).

### Minimum interval between Stack executions

The minimum interval for a Job is based on the number of Blocks you can execute in a month, which is the measurement that billing is based on. Currently, all accounts are free and are allowed 150,000 block executions per month. This works out to approximately 200 block executions per hour.

There is also a hard limit of 1 min for the interval - a Job can not be scheduled more often than this.

So as an example, with a default free account you could have two active stacks with 5 blocks in each stack (10 active blocks total), which each run every 3 minutes.

Or you could have 10 stacks with two blocks each, (20 active blocks total) which each run every 6 minutes, etc.

You will receive a warning/error if you try to enable a stack which would result in your monthly execution allowance being exceeded. You may optionally enable the jobs anyways, however once your monthly allowance is exhausted, your jobs will cease to execute for the remainder of the month, so be wary when enabling jobs that will put you over the limit.

### One-time Jobs and Scheduled Jobs

You can also create jobs which are one-time jobs: that is, they will only execute once on the specified day and time, and then disable themselves.

Alternately, instead of 'every # minutes', you can create jobs which will run daily/weekly/monthly at an appointed day and/or time.

One-time and Scheduled Jobs will count towards the usual monthly allowance for total number of block executions.

### Running Stacks manually or via the web ("Webhooks")

Stacks can also be run directly through the web interface (*coming soon*), or via the unique url assigned to the stack.

See the [Advanced Topics - Webhooks and Manually Triggered Stacks](../Advanced_Topics/Webhooks.md) section for more details.

##### [Next Topic: The Basics - Sharing Stacks](./Sharing_Stacks.md)

##### [Previous Topic: The Basics - Blocks](./Blocks.md)

### [Back to Table of Contents](../README.md)

