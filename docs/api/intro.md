Our MicroPasts installation of Pybossa provides two API endpoints:

* the RESTful API located at https://crowdsourced.micropasts.org/api and,
* the rest of the endpoints of the server

In this guide, we only cover the basics of what Pybossa can achieve and the calls
that are likely to be needed for analysis work.  For fuller
documentation refer to the [official documentation from Scifabric](https://docs.pybossa.com/api/intro/)

## The RESTful API

The JSON based RESTful API is located at:

    https://crowdsourced.micropasts.org/api

These endpoints use PYBOSSA domain objects. It has the following structure:

    https://crowdsourced.micropasts.org/api/{domainobject}[?arg1=value1&arg2=value2...]

A domain object will be one of the following:

* Project
* Task
* TaskRun
* Result
* Blog post
* Announcement
* Helpingmaterial
* User
* Category.

You can then access any of those items via the API, using its name in the {domainobject} section and querying its attributes.

Some requests will need a **API-KEY** to authenticate & authorize the operation. You can get your API-KEY in your *profile* account.

The returned objects will have a **links** and **link** fields, not
included in the model to support [Hypermedia as the Engine of
Application State](http://en.wikipedia.org/wiki/HATEOAS) (also known as HATEOAS), so you can understand which are the relations between objects.

All objects will return a field **link** which will be the absolute URL for that specific object within the API. If the object has some parents, you will find the relations in the **links** list. For example, for a Task Run you will get something like this:

```json
{
"info": 65,
"user_id": null,
"links": [
    "<link rel='parent' title='project' href='https://crowdsourced.micropasts.org/api/project/90'/>",
    "<link rel='parent' title='task' href='https://crowdsourced.micropasts.org/api/task/5894'/>"
],
"task_id": 5894,
"created": "2012-07-07T17:23:45.714184",
"finish_time": "2012-07-07T17:23:45.714210",
"calibration": null,
"project_id": 90,
"user_ip": "X.X.X.X",
"link": "<link rel='self' title='taskrun' href='https://crowdsourced.micropasts.org/api/taskrun/8969'/>",
"timeout": null,
"id": 8969
}
```

* The *object* link will have a tag **rel** equal to **self**, while the
parent objects will be tagged with **parent**. The **title** field is
used to specify the type of the object: task, task run or project.
* Projects will not have a **links** field because these objects do not
have parents.
* Tasks will have only one parent: the associated project.
* Task Runs will have only two parents: the associated task and associated project.
* Results will have two parents: the associated project and task.
* Helping materials will have only one parent: the project.
* Blog posts will have only one parent: the project.
* Announcements will not have any parent.

### Rate Limit and the API

Rate Limiting has been enabled for all the API endpoints (since PYBOSSA v2.0.1).
Rate-limiting gives any user, using the IP, **a window of 15 minutes to do at most 300 requests per endpoint**.

This feature adds to the request headers the following values:

-   **X-RateLimit-Limit**: the rate limit ceiling for that given request
-   **X-RateLimit-Remaining**: the number of requests left for the 15
    minute window
-   **X-RateLimit-Reset**: the remaining window before the rate limit
    resets in UTC epoch seconds

We recommend using the Python package **requests** for interacting with PYBOSSA,
as it is straightforward to check those values:

```python
import requests
import time

res = requests.get('https://crowdsourced.micropasts.org/api/project')
if int(res.headers['X-RateLimit-Remaining']) < 10:
    time.sleep(300) # Sleep for 5 minutes
else:
    pass # Do your stuff
```

### Command line Example Usage of the API


Create a Project object:

```
curl -X POST -H "Content-Type:application/json" -s -d '{"name":"myproject", "info":{"xyz":1}}' 'https://crowdsourced.micropasts.org/api/project?api_key=API-KEY'
```

## PYBOSSA endpoints

These are the endpoints that you usually visit using the pybossa-default-theme or in other words, the routes that are defined in PYBOSSA for the user.

For example, while you can get information about a project using the  RESTful API:

    https://crowdsourced.micropasts.org/api/project?short_name=foo

You can also get information about the project using the following URL:

    https://crowdsourced.micropasts.org/project/foo/

That URL by default it will return HTML, but you can ask the server to return it in JSON format. While the RESTful API only returns the objects as they are in the database, these other endpoints can return more data, like for example the list of projects a user has participated.

!!! tip "Single Page Applications (SPA)"
     If you want to build a SPA, when you create the routes, please, re-use the PYBOSSA ones as it will be easier for you to follow redirects. Also, this is important for other parts, like validation of emails, etc. Otherwise, you will have to handle those redirections in your web server.
