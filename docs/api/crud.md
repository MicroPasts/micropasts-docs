# Introduction
This section shows the CRUD methods (Create, Read, Update and Delete) that you can use using the RESTful API. These methods will not work with the rest of the PYBOSSA endpoints, as that only support GET and POST actions.

## Getting data

You can get a specific domain object by its id (by default any GET action will return only 20 objects, you can get more or fewer objects using the **limit** option):

    GET https://crowdsourced.micropasts.org/api/{domain-object}/{id}[?api_key=API-KEY]

!!! note
    Some GET actions may require to authenticate & authorize the request. Use the "?api_key" argument to pass the **API-KEY**.

If the object is not found you will get a JSON object like this:

```json
{
    "status": "failed",
    "action": "GET",
    "target": "project",
    "exception_msg": "404 Not Found",
    "status_code": 404,
    "exception_cls": "NotFound"
}
```

Any other error will return the same object but with the proper status code and error message.

## Filtering the data

You can get a list of domain objects by its fields. You will use the objects attributes to filter the query, as it will return only objects matching it:

    GET http://pybossa-site-url/api/{domain-object}[?domain-object-field=value]

Multiple fields can be used separated by the **&** symbol:

    GET http://pybossa-site-url/api/{domain-object}[?field1=value&field2=value2]

It is possible to limit the number of returned objects:

    GET https://crowdsourced.micropasts.org/api/{domain-object}[?field1=value&limit=20]

### Filtering info keys for PYBOSSA versions up to v2.8.X

It is possible to access first level JSON keys within the **info** field
of Categories, Projects, Tasks, Task Runs and Results:

    GET https://crowdsourced.micropasts.org/api/{domain-object}[?field1=value&info=foo::bar&limit=20]

To search within the first level (nested keys are not supported), you
have to use the following format:

    info=key::value

For adding more keys:

    info=key1::value1|key2::value2|keyN::valueN

These parameters will be ANDs, so, it will return objects that have
those keys with and **and** operator.

### Filtering info keys for PYBOSSA >= 2.9.1 (or master branch)

Since version v2.9.0, PYBOSSA stores JSON data as JSONB within the database.

This feature allows us to use new queries, like path searching via the API.

You can now search like this:

    info={"key1": value}
    info={"key1": [{"key2": value}]}

You specify the JSON path and the values, and the system will return those that meet
that criteria. This new feature allows you to filter nested data without any problems.

If you are not storing a dictionary, {}, in the info field, you will need to quote your
string, so the sytem can handle it properly. If you are storing a number, instead of a
string, then you should use '"9"' double quotes. This applies to integers and floats.


### Your data first

The API understands who is requesting the data. When you are using your API key to authenticate your calls, the system will return only your data. For instance, if you request all the projects, you will only get **your projects** and the rest (owned by other users will be hidden).  You can get access to all the projects, tasks, and task runs (the whole database) using the parameter: **all=1**.

For example, if an anonymous user accesses the generic API endpoints like:

    GET https://crowdsourced.micropasts.org/api/project

It will return all the projects from the DB, ordering them by ID. If you
access it like authenticating yourself:

    GET https://crowdsourced.micropasts.org/api/project?api_key=YOURKEY

Then, you will get your projects. In other words, the
projects that you own. If you don't have a project, but you want to
explore the API then you can use the **all=1** argument:

    GET https://crowdsourced.micropasts.org/api/project?api_key=YOURKEY&all=1

This call will return all the projects from the DB ordering by ID.

For example, you can get a list of your Projects like this:

    GET https://crowdsourced.micropasts.org/api/project
    GET https://crowdsourced.micropasts.org/api/project?api_key=YOURKEY
    GET https://crowdsourced.micropasts.org/api/project?api_key=YOURKEY&all=1

Or a list of available Categories:

    GET https://crowdsourced.micropasts.org/api/category

Or a list of Tasks:

    GET https://crowdsourced.micropasts.org/api/task
    GET https://crowdsourced.micropasts.org/api/task?api_key=YOURKEY
    GET https://crowdsourced.micropasts.org/api/task?api_key=YOURKEY&all=1

For a list of TaskRuns use:

    GET https://crowdsourced.micropasts.org/api/taskrun
    GET https://crowdsourced.micropasts.org/api/taskrun?api_key=YOURKEY
    GET https://crowdsourced.micropasts.org/api/taskrun?api_key=YOURKEY&all=1

### Using date ranges

If you want, you can use the domain attribute *created* to get items from the DB. You can specify a year, year-month, year-month-day to get all the values for those ranges. For example, if you want all the tasks that have been created in 2015, just use:

    GET https://crowdsourced.micropasts.org/api/task?created=2015&all=1

If you want all the task runs from 2015-05:

    GET https://crowdsourced.micropasts.org/api/taskrun?created=2015-05&all=1

You can, for example, get all the task runs that a user has submitted on a given day like this:

    GET https://crowdsourced.micropasts.org/api/taskrun?created=2015-05-03&user_id=3

This filter works for any object that has the *created* attribute.

## Order by
Any query can be ordered by an attribute of the domain object that you are querying. For example, you can get a list of tasks ordered by ID:

    GET https://crowdsourced.micropasts.org/api/task?orderby=id

If you want, you can order them in descending order:

    GET https://crowdsourced.micropasts.org/api/task?orderby=id&desc=true

Check all the attributes that you can use to order by in the [Domain
Object section](https://github.com/Scifabric/pybossa/tree/master/pybossa/model).

!!! note
    Please, notice that to keep users privacy, only their locale and nickname will be shared by default. Optionally, users can disable privacy mode in their settings. By doing so, also their full name and account creation date will be visible for everyone through the API.

## Limit

By default, PYBOSSA limits the list of items to 20. If you want to get more items, use the keyword **limit=N** with **N** being a number to get that amount. There is a maximum of 100 to the **limit** keyword, so if you try to get more items at once, it won't work.

## Pagination
You can paginate the results of any GET query using the last ID of the domain object that you have received and the parameter:     **last_id**. For example, to get the next 20 items after the last   project ID that you've received, you will write the query like this:

    `GET /api/project?last_id=N.

!!! info "Pagination with offset and limit"
    **DEPRECATED (see next Note for a better and faster solution)**  You can use the keyword **offset=N** in any **GET** query to skip that many rows before beginning to get rows. If both **offset** and **limit** appear, then **offset** rows are skipped before starting to count the **limit** rows that are returned.

## Related data

For Tasks, TaskRuns, and Results you can get the associated data using the argument: *related=True*.

This flag will allow you to get in one call all the TaskRuns and Result
for a given task. You can do the same for a TaskRun getting the Task and associated Result, and for a Result getting all the TaskRuns and
associated Task.

Projects do not have this feature, as it will be too expensive for the
API.

## Project stats

For Projects, you can get the associated statistics using the argument: *stats=True*.

This will add the statistics, such as overall progress, number of tasks etc.,
against the `stats` key for each project.

## Create

Create a domain object. Returns created domain object.:

    POST https://crowdsourced.micropasts.org/api/{domain-object}[?api_key=API-KEY]

!!! note
    Some POST actions may require to authenticate & authorize the request. Use the
    `?api_key` argument to pass the **API-KEY**.

If an error occurs, the action will return a JSON object like this:

```json
{
    "status": "failed",
    "action": "POST",
    "target": "project",
    "exception_msg": "type object 'Project' has no attribute 'short_ame'",
    "status_code": 415,
    "exception_cls": "AttributeError"
}
```

Where **target** will refer to a Project, Task or TaskRun object.

## Update

Update a domain object:

    PUT https://crowdsourced.micropasts.org/api/{domain-object}/{id}[?api_key=API-KEY]

!!! note
    Some PUT actions may require to authenticate & authorize the request. Use the
    ?api_key argument to pass the **API-KEY**.

If an error occurs, the action will return a JSON object like this:

```json
{
    "status": "failed",
    "action": "PUT",
    "target": "project",
    "exception_msg": "type object 'Project' has no attribute 'short_ame'",
    "status_code": 415,
    "exception_cls": "AttributeError"
}
```

Where **target** will refer to a project, Task or TaskRun object.

## Delete

Delete a domain object:

    DELETE https://crowdsourced.micropasts.org/api/{domain-object}/{id}[?api_key=API-KEY]

!!! note
    Some DELETE actions may require to authenticate & authorize the request. Use the
    `?api_key` argument to pass the **API-KEY**.

If an error occurs, the action will return a JSON object like this:

```json
{
    "status": "failed",
    "action": "DELETE",
    "target": "project",
    "exception_msg": "type object 'Project' has no attribute 'short_ame'",
    "status_code": 415,
    "exception_cls": "AttributeError"
}
```

Where **target** will refer to a Project, Task or TaskRun object.


