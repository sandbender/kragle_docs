# Kragle.io Documentation

### [Table of Contents](../README.md)

##### [Previous Topic: Jobs](./Jobs.md)

##### [Next Topic: Validation](./Validation.md)

## API Reference - Schedules

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
            "config": {"period": <number of seconds in between executions for Jobs using this schedule>}
        },
        ...
    }
}
```

**Required parameters**:
  - None

**Optional parameters**:
  - `page` - integer - The page # of the result set you wish to retrieve. Default: 1.

### GET /schedules/byid/\<job id>

_(This endpoint does **not** require authentication.)_

Retrieve information describing a single schedule, looked up by id.

The job is returned as a JSON response body following this format:
```
{
    "id": <unique schedule id>,
    "name": <human readable name for this schedule>,
    "config": {"period": <number of seconds in between executions for Jobs using this schedule>}
}
```

**Required parameters**:
  - None

**Optional parameters**:
  - None

##### [Next Topic: Validation](./Validation.md)

##### [Previous Topic: Jobs](./Jobs.md)

### [Back to Table of Contents](../README.md)

