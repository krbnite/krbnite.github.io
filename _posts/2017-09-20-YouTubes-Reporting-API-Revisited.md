---
layout: post
title: YouTube's Reporting API Revisited
tags: youtube python wwe
---

## Reader Beware
For some motivation and preliminary reading, you might find my 
[original exploratory post](https://krbnite.github.io/The-YouTube-Reporting-API/)
on YouTube's Reporting API helpful.  In this particular post, I just want to cover some additional
technical details that I'm more aware of after having covered 
[Google's Client API](https://krbnite.github.io/The-Google-Client-API-for-Python/) and
[YouTube's Data API](https://krbnite.github.io/The-YouTube-Data-API/).


## Nothing Much To It!
Unlike [YouTube's Data API](https://krbnite.github.io/The-YouTube-Data-API/), 
there is very little to learn about the Reporting API.  This simplicity is intentional: the API
is intended only for (i) listing what types of reports YouTube provides, (ii) listing which jobs
you currently have running (i.e., which reports do you currently have YouTube generating for you
on the daily), (iii) listing the reports associated with each job (essentially a list of URLs associated
with the daily reports), and (iv) creating new jobs.  

Importantly, the Reporting API is not meant to be an interactive querying tool.  Such a use case is
why you might turn to YouTube's Analytics API.  The Reporting API is basically meant to tell YouTube,
"I want all the data everyday."  YouTube says, "Ok, we'll definitely give you a shitload, but not quite
the full fuckload."  And you say, "Yea, whatever -- as much as possible. Everyday." 

Then, everyday, you check in on YouTube: "Got my shit?" 

You might one day realize that you don't need some of the reports, or that there is one that you 
have not been getting.  Again, you use the Reporting API to correct this.  

So when do you actually use the Analytics API?  YouTube might tell you, "Whenever you need to do
a quick, targeted query."  Indeed, the Analytics API allows for some targeting and it is interactive, 
but here's the thing: if you have the Reporting API giving you all the data, everyday, and you are
warehousing that data (say, in Amazon Redshift), then you can just target queries to your heart's desire
in R, Python, or whatever your favorite programming environment is at the time.  

## Quick Recap 
Here is how to set everything up.
```python
from oauth2client.client import flow_from_clientsecrets
from oauth2client.tools import run_flow
from oauth2client.file import Storage
from apiclient.discovery import build
import httplib2

SCOPE = 'https://www.googleapis.com/auth/yt-analytics.readonly'
CLIENT_SECRETS_FILE = "path/to/client_secrets.json" 
CREDENTIALS_FILE = 'path/to/name-of-OAuth2-file-if-you-made-one.json' # e.g., test-oauth2.json
SERVICE_NAME = "youtubereporting"
VERSION = 'v1'

flow = flow_from_clientsecrets(CLIENT_SECRETS_FILE, scope=SCOPE, message=' f off ')
storage = Storage(CREDENTIALS_FILE) 
credentials = storage.get()   # Returns None if the file doesn't exist
if credentials is None or credentials.invalid:
    credentials = run_flow(flow, storage)
    
report = build(SERVICE_NAME,  VERSION,  http=credentials.authorize(httplib2.Http()))
```

## Jobs
The [jobs resource](https://developers.google.com/youtube/reporting/v1/reference/rest/v1/jobs) of
the Reporting API is your way of telling YouTube what reports you want too see at your doorstep
every morning.

The jobs resource primarily supports 4 methods:
* You can tell YouTube, "Hey, I want this other report too," with the [create](https://developers.google.com/youtube/reporting/v1/reference/rest/v1/jobs/create) method
* You can say, "Ya know what?  I don't want that report anymore," with the [delete](https://developers.google.com/youtube/reporting/v1/reference/rest/v1/jobs/delete) method
* If you know the secret password (the jobId), you can use the 
[get](https://developers.google.com/youtube/reporting/v1/reference/rest/v1/jobs/get) method to ask, 
"Hey YouTube, tell me about that one job I have you doing?"
* If you don't remember any secret passwords, you can bully YouTube into squealing with the 
[list](https://developers.google.com/youtube/reporting/v1/reference/rest/v1/jobs/list) method


### Example: List Current Jobs
```python
# Jobs associated w/ user's channel
report.jobs().list().execute()

# Jobs associated w/ content owner
report.jobs().list(onBehalfOfContentOwner='yourContentOwnerId')
```
Each job that is listed has the following properties:
* createTime (*YYYY-MM-DDThh:mm:ssZ*): the time at which the job was created
    - time in [ISO 8601](https://www.w3.org/TR/NOTE-datetime) format
    - specifically, this means that the timezone for createTime is Universal Time (UT)
* id: the job ID
* name: the name given to the job, e.g., 'User activity'
* reportId: YouTube id for the report associated with the job, e.g., content\_owner\_basic\_a3

If you have more jobs than YouTube is willing to show you at once, it will return another response property called
`nextPageToken`.  If this is the case, you will need to issue another command to see more of your jobs:
```python
nextPage = previousResponse['nextPageToken']
report.jobs().list(onBehalfOfContentOwner='yourContentOwnerId', pageToken=nextPage)
```

## List Reports Generated by a Specific Job
Here we list the reports 
on behalf of a content owner for jobId created after on or after 2017-09-21.

```python
report.jobs().reports().list(
    jobId=jobId, 
    onBehalfOfContentOwner=contentOwnerId, 
    createdAfter='2017-09-21T00:00:00Z'
).execute()
```

You should see that each report in the response has several properties, including:
* id: unique report ID 
* jobId: id of the job that generated the report
* startTime, endTime: the data coverage period
    - days are always bucketed in Pacific Time, so the timestamp will vary between Pacific Daylight Time (PDT = UT-7, e.g., *YYYY-MM-DDT07:00:00Z*) and Pacific Standard Time (PST = UT-8, e.g., *YYYY-MM-DDT08:00:00Z*) throughout the year
* createTime (*YYYY-MM-DDThh:mm:ss.xxxxxZ*): the time at which the report was created
    - time in [ISO 8601](https://www.w3.org/TR/NOTE-datetime) format
    - specifically, this means that the timezone for createTime is Universal Time (UT)
* jobExpireTime: the time at which the job is set to expire (or has expired)
    - most jobs run indefinitely and will not have this property
* downloadUrl: the URL at which you can download the report using the `.get` method 


## Retrieve a Report
```python
from apiclient.http import MediaIoBaseDownload
from io import FileIO

response = report.jobs().reports().list(
    jobId = jobId, 
    onBehalfOfContentOwner = contentOwnerId,
    startTimeAtOrAfter = '2017-09-12T00:00:00Z'
).execute()

for entry in response['reports']:
    request = report.media().download(resourceName="csv")
    request.uri = entry['downloadUrl']
    dataDate = entry['startTime'][0:10]
    createDate = entry['createTime'][0:10]
    reportName = 'report-' + dataDate + '-as-on-' + createDate + '.csv' 
    print('Downloading the', dataDate, 'Report (created on,', createDate, ')')

    # Stream/download the report in a single request. 
    chunksize = 1e8   # you can put -1, but I find using a number like this shows you more updates
    fh = FileIO(reportName, mode='wb')
    downloader = MediaIoBaseDownload(fh, request, chunksize=chunksize)
    done = False 
    while done is False: 
        status, done = downloader.next_chunk() 
        if status: 
            print("Download %d%%." % int(status.progress() * 100))
    print("Download Complete!")
```

## Not Getting Enough Data? 
The Reporting API gives you access to a lot of reports.  You might want to make sure 
you're getting everything you want.
```python
# List all reports available to you as a channel owner
report.reportTypes().list().execute()

# List all reports available to you acting on behalf of a content owner
report.reportTypes().list(onBehalfOfContentOwner=contentOwnerId).execute()
```

## Create a New Job on Behalf of a Content Owner
To create a new job, we must (i) know the `reportId` of the report we'd like see generated daily,
(ii) `name` the job (so we don't forget what it is), and (iii) put these (key,value) pairs into
a dictionary that we pass to the `body` parameters of the `create` method.

The `create` method accepts one optional parameter: `onBehalfOfContentOwner`.  If this parameter is not 
set, then YouTube assumes you want the job done for the YouTube channel associated with your
verified/authenticated Google account.  However, if you are or work for some media company that owns
a bunch of channels, you might want the report to cover all of those channels at once!


```python
contentOwnerId = 'topSecretStringOfLettersAndNumbers!'
reportId = 'content_owner_basic_a3'
reportName = 'User Activity'
report.jobs().create(
    body = {'reportTypeId': reportId, 'name': reportName},
    onBehalfOfContentOwner=contentOwnerId
    ).execute()
```

Oops!  Did you get a 403 Error?  Here's the error I get from Python:
> HttpError: <HttpError 403 when requesting 
> 'ht&#8203;tps://youtubereporting.googleapis.com/v1/jobs?onBehalfOfContentOwner=topSecretStringOfLettersAndNumbers!=json
> returned "The caller does not have permission">

From the Reporting API documentation:
> *Forbidden (403)*
> The request attempted to create a job for a system-managed report. 
> YouTube will automatically generate system-managed reports, and content owners 
> will not be able to modify or delete jobs that create those reports.

