---
layout: post
title: The Google Client API
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

## The Build
In the previous section, to access the Reporting API, we used the function `build` from 
`apiclient.discovery`.  This is the essence of accessing any Google service.

```python
from apiclient.discovery import build
import httplib2
SERVICE_NAME = "youtubereporting"
VERSION = 'v1'
reporting_api = build(SERVICE_NAME,  VERSION,  http=credentials.authorize(httplib2.Http()))
```

