# Kragle.io Documentation

### [Table of Contents](../README.md)

##### [Previous Topic: Jobs](./Jobs.md)

##### [Next Topic: Validation](./Validation.md)

## API Reference - Schedules

### Valid elements of a 'config' structure

The 'config' structure is returned/used as part of a Schedule specification, is a dictionary (object), and may contain the following keys:
  - `period` - integer - The number of seconds in between successive Stack executions, for Stacks which use this schedule.
  - `timestamp` - integer - a Unix Timestamp (seconds since Jan 1 1970 UTC), which is the target time for this one-time schedule to execute

### GET /schedules

_(This endpoint does **not** require authentication.)_

Retrieve a list of available Schedules for use when creating Jobs for your Stacks.

Pagination is supported via the `page` parameter.

Results are returned as a JSON response body, following this format:
```
{
    "count": <total # of results>,
    "pagesize": <# of results on this page (10)>,
    "page": <current page>,
    "schedules": [
        {
            "id": <unique schedule id>,
            "name": <human readable name for this schedule>,
            "once": <boolean - is this a one-time schedule?>,
            "config": {"period": <number of seconds in between executions for Jobs using this schedule>}
        },
        ...
    }
}
```

**NB**: 'config' may contain elements other than 'period'... see above for possible elements/values in this structure.

**Required parameters**:
  - None

**Optional parameters**:
  - `page` - integer - The page # of the result set you wish to retrieve. Default: 1.

### GET /schedules/byid/\<schedule id>

_(This endpoint does **not** require authentication.)_

Retrieve information describing a single schedule, looked up by id.

The schedule is returned as a JSON response body following this format:
```
{
    "id": <unique schedule id>,
    "name": <human readable name for this schedule>,
    "once": <boolean - is this a one-time schedule?>,
    "config": {"period": <number of seconds in between executions for Jobs using this schedule>}
}
```

**NB**: 'config' may contain elements other than 'period'... see above for possible elements/values in this structure.

**Required parameters**:
  - None

**Optional parameters**:
  - None

### POST /schedules

_(This endpoint requires authentication.)_

Create a new Schedule.

The request body must be a JSON object whose top level properties correspond to the required and optional parameters described below.

On success, the id of the newly created Schedule is returned in a JSON response body like this:
```
{
    "id": <new Schedule id>
}
```

**Required parameters**:
  - `config` - object - A config object conforming to the description provided at the top of this page.

**Optional parameters**:
  - `name` - string - A human-readable name for this Schedule. Default: None.
  - `once` - boolean - Whether this will be a one-time schedule or not. Default: False.

### PUT /schedules/<schedule id>

_(This endpoint requires authentication.)_

Used to update the parameters of an existing Schedule.

The request body should be a JSON object whose top level properties correspond to the required and optional parameters described below.

On success, a JSON object is returned as the response body, following this format:
```
{
    "id": <unique schedule id>,
    "name": <human readable name for this schedule>,
    "once": <boolean - is this a one-time schedule?>,
    "config": {"period": <number of seconds in between executions for Jobs using this schedule>}
}
```

**NB**: 'config' may contain elements other than 'period'... see above for possible elements/values in this structure.

**Required parameters**:
  - None

**Optional parameters**:
  - `config` - object - A config object conforming to the description provided at the top of this page.
  - `name` - string - A human-readable name for this Schedule. Default: None.
  - `once` - boolean - Whether this will be a one-time schedule or not. Default: False.

##### [Next Topic: Validation](./Validation.md)

##### [Previous Topic: Jobs](./Jobs.md)

### [Back to Table of Contents](../README.md)

