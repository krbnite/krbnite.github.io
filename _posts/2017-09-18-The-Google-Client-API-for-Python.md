---
layout: post
title: The Google Client API
tags: youtube python wwe
---

Previously, I wrote about how to use the YouTube Reporting API from Python. My hope
was that by dissecting and restructuring the provided code snippets into a more procedural
format, it would be easier for a newcomer to get a sense of what each line of code is
doing... Or at the least, allow them to use the code in an interactive python session 
to see what each line does (whether or not they gain any further sense of it).

In this post, I want to go over the basics of the Google Client API itself, which
can really help elucidate the YouTube Reporting API code.  Better, understanding a 
bit of basics of this library is transferrable to all other services accessible through
this library -- BigQuery, Google Analytics, etc.

Good shit -- let's go!

## Get the API
```
pip install --upgrade google-api-python-client
```

Also:
```
pip install --upgrade oauth2client
```
...though I just found out it has been [deprecated](https://pypi.python.org/pypi/oauth2client), which
is delightlful.  Fret not: it still works.  The Reporting and Data API documentation both use it.

## The Build
In the previous section, to access the Reporting API, we used the function `build` from 
`apiclient.discovery`.  This is the essence of accessing any Google service.

```python
from apiclient.discovery import build
import httplib2
SERVICE_NAME = "youtubereporting"
VERSION = 'v1'
reporting_api = build(SERVICE_NAME,  VERSION,  
    http=credentials.authorize(httplib2.Http()))
```

## Flow, Credentials, and Client Secrets
The next part of the code to understand derives from the `oauth2client` library, which you
might argue is not the `apiclient` library and ultimately win the argument.  But as it says
in its help file, `oauth2client` is a
> "client library for using OAuth2, especially with Google APIs."

Good enough for this post.

In the code, we import `flow_from_clientsecrets` which we use to define a variable called `flow`.
What is it for?  Well, you might notice that `flow` is only used if the acquired credentials
are invalid or simply do not exist.  Let's assume your credentials should are valid. Then, the only reason
your credentials wouldn't exist is because this is the first time you are using this code in your project.
The if statement catches this and calls `run_flow`, which creates your credentials for the current
session and the credentials file for future sessions.

So, basically, your client\_secrets.json file is used to create your credentials file the first 
time you use the API.  From there on out, you really only need the credentials file.

```python
from oauth2client.client import flow_from_clientsecrets
from oauth2client.tools import run_flow
from oauth2client.file import Storage

SCOPE = 'https://www.googleapis.com/auth/yt-analytics.readonly'
CLIENT_SECRETS_FILE = "path/to/client_secrets.json" 
CREDENTIALS_FILE = 'path/to/name-of-OAuth2-file-if-you-made-one.json' # e.g., test-oauth2.json

flow = flow_from_clientsecrets(CLIENT_SECRETS_FILE, scope=SCOPE, message=' f off ')
storage = Storage(CREDENTIALS_FILE) 
credentials = storage.get()   # Returns None if the file doesn't exist
if credentials is None or credentials.invalid:
    credentials = run_flow(flow, storage)
```

Knowing this, the command line python scripts provided in the Reporting API documentation really
start making sense.  

## Collections
Straight from the horse's documentation:
> Each API service provides access to one or more resources. A set of resources of the same 
> type is called a collection. The names of these collections are specific to the API. The 
> service object is constructed with a function for every collection defined by the API.

So if the given API has a collection named reportTypes, you create the collection object like this:
```python
reportTypes = reporting_api.reportTypes()
```

Collections can have methods.  For example, the reportTypes collection might have a list method:
```python
listOfReports = reportTypes.list()
```

However, this collection method does not actually call the API.  It waits to be told to do so by the `.execute()` method:
```
results = listOfReports.execute()
```

Putting this altogether, we have another line of code from the previous post:
```python
results = reporting_api.reportTypes().list().execute()
```

The results always return a JSON file, which is a dictionary inside Python.

Each API has its own collections.  The YouTube Reporting API, for example, also has the `jobs` collection,
the `media` collection, and the `new_batch_http_request` collection (though I'm not sure if this last one
is considered a collection or not...probably). 

Each collection has its own methods:
* reportTypes: list, list\_next
* jobs: create, delete, get, list, list\_next, reports
* media: download, download\_media
* new\_batch\_http\_request: add, execute 

