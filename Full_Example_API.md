# Kragle.io Documentation

### [Table of Contents](./README.md)

##### [Previous Topic: Faults and Error Codes](./API_Reference/Faults.md)

## A Full Example/Walkthrough, via the API

**NB:** All the following code is written with Python 2.7 and utilizes the 'requests' and 'json' libraries. However all the examples should be easily adaptable to any other language with support for basic HTTP requests and json encoding/decoding.

In this example, we'll create a connection between a Delicious.com account and a Hipchat room.

We want to have Kragle post a message to the Hipchat room, which contains some of our coworkers, whenever we post a new url to Delicious that's tagged with "coworkers".


##### [Previous Topic: Faults and Error Codes](./API_Reference/Faults.md)

### [Back to Table of Contents](./README.md)
