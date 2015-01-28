# Kragle.io Documentation

### [Table of Contents](../README.md)

##### [Previous Topic: Webhooks](./Webhooks.md)

##### [Next Topic: Schedules](./Schedules.md)

## API Reference - Jobs

### GET /jobs

_(This endpoint requires authentication.)_

Retrieve a list of your jobs.

Pagination is supported via the `page` parameter.

Results are returned as a JSON response body, following this format:
```
{
    "count": <total # of results>,
    "pagesize": <# of results on this page (10)>,
    "page": <current page>,
    "jobs": [
        {
            "id": <unique job id>,
            "name": <human readable name for this job>,
            "stack": <the id of the Stack to which this job applies>,
            "schedule": <the id of the Schedule that this job uses>,
            "do_run": <boolean indicating whether the job is active>
        },
        ...
    }
}
```

**Required parameters**:
  - None

**Optional parameters**:
  - `page` - integer - The page # of the result set you wish to retrieve. Default: 1.

### GET /jobs/byid/\<job id>
### GET /jobs/byname/\<job name>

_(This endpoint requires authentication.)_

Retrieve information describing a single job, looked up by id or name.

The job is returned as a JSON response body following this format:
```
{
    "id": <unique job id>,
    "name": <human readable name for this job>,
    "stack": <the id of the Stack to which this job applies>,
    "schedule": <the id of the Schedule that this job uses>,
    "do_run": <boolean indicating whether the job is active>
}
```

**Required parameters**:
  - None

**Optional parameters**:
  - None

### POST /jobs

_(This endpoint requires authentication.)_

Create a new Job.

The request body must be a JSON object whose top level properties correspond to the required and optional parameters described below.

On success, the id of the newly created Job is returned in a JSON response body like this:
```
{
    "id": <new Job id>
}
```

**Required parameters**:
  - `schedule_id` - integer - The id of the Schedule to use for this Job.
  - `stack_id` - integer - The id of the Stack to attach this Job to.

**Optional parameters**:
  - `name` - string - A human-readable name for this job. Default: None.
  - `enabled` - boolean - Whether or not this Job should be active upon creation. Default: false.

### PUT /jobs/\<job id>

_(This endpoint requires authentication.)_

Used to update the parameters of an existing Job.

The request body should be a JSON object whose top level properties correspond to the required and optional parameters described below.

On success, a JSON object is returned as the response body, following this format:
```
{
    "owner": <id of the owner - this will always be your user id>,
    "name": <human-readable name of the Job>,
    "stack": <the id of the Stack to which this Job is attached>,
    "schedule": <the id of the Schedule which this Job uses>,
    "do_run": <boolean indicating whether this Job is currently active or not>,
    "do_del": <boolean indicating whether this Job is currently queued for deletion>
}
```

**Required parameters**:
  - None

**Optional parameters**:
  - `schedule_id` - integer - The id of the Schedule to use for this Job.
  - `stack_id` - integer - The id of the Stack to attach this Job to.
  - `name` - string - A human-readable name for this job.
  - `enabled` - boolean - Whether or not this Job should be active.

**NB**: Any optional parameters **not** passed in the request will be left unchanged.

DELETE /jobs/\<job id>

_(This endpoint requires authentication.)_

Used to delete a Job (or queue a Job for deletion depending on it's current enabled status).

On success, one the following JSON response bodies will be returned:
```
{
    "queued_for_deletion": <id of Job to be deleted>
}
```
**or**
```
{
    "deleted": <id of Job which was deleted>
}
```

**NB:** Jobs are not always deleted *immediately*: if the Job is currently active and queued for another run, it will be deleted (instead of executed) at it's appointed next-run time. Otherwise, it will be deleted immediately.

##### [Next Topic: Schedules](./Schedules.md)

##### [Previous Topic: Webhooks](./Webhooks.md)

### [Back to Table of Contents](../README.md)

