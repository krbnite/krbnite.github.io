---
layout: post
title: The YouTube Reporting API
---

Scraping is great for views, likes, dislikes, comments and sometimes a few other quantities.  The reason for scraping
is timeliness: it affords you to get up-to-minute estimates of your content's viewership without waiting for YouTube to
officially release estimates of valid views in 2-3 days.  Scraping itself might not even be necessary for small players
in the media game:  one can use YouTube's real-time analytics if they do not frequently release
 too much content (I think it tracks the lateste 20 uploads).  But if you work at a media company that releases
15-50 videos every day, this option becomes less useful -- thus scraping (which I covered in several previous
posts).  

If timeliness is not a factor, scraping might not be the best option since
you can technically use YouTube analytics and get more info than you can scrape.  That said, you can still 
"scrape" if selenium is in your arsenal by creating your own API that can sign into your analytics.youtube.com 
account and scrape around.  

But still, is that the best way? Or just a cool way?

For example, YouTube offers the [Analytics API](https://developers.google.com/youtube/analytics/v1/data_model) for 
targeted queries against viewership and ad performance data.  If you can sign into to the CMS and do some point-and-click
to find what you seek (or seleniumize that), then you can probably use the Analytics API.  


That said, if you want slice-and-dice the data 
in multiple ways, you might find yourself making programs with all kinds of for-loops to account for different dimensions,
metrics, and filters. If you have only one YouTube channel, this might still seem ok... But not if you are a content
owner with hordes YouTube channels. 

As a content owner (or data scientist working for one), wouldn't it be nice to just have all of the data in
your own database, which you can query however you want and bring into your favorite programming environment?

Well, that's basically what the [YouTube Reporting API](https://developers.google.com/youtube/reporting/v1/reports/) 
is for, at least in terms of your channels' viewership and ad-performance data.  For other types of data, like
comments, there exist other APIs (e.g., the Data API and the Content ID API).  For now, forget about that shit, lest
your head explode.  Fact is, figuring out how to use these APIs can be tricky at first, but once you figure out 
one, learning the others is simple and straightforward.

```python
from apiclient.discovery import build
from oauth2client.client import flow_from_clientsecrets
from oauth2client.tools import run_flow
from oauth2client.file import Storage
import httplib2

SERVICE_NAME = "youtubereporting"
VERSION = 'v1'
SCOPE = 'https://www.googleapis.com/auth/yt-analytics.readonly'
CLIENT_SECRETS_FILE = "client_secrets.json"  # Presuming you made this and in dir w/ it
flow = flow_from_clientsecrets(CLIENT_SECRETS_FILE, scope=SCOPE, message=' f off ')
storage=Storage('test-oauth2.json')
credentials = run_flow(flow, storage)
# If first time, your browser will open up.... Choose account....
my_build = build(SERVICE_NAME,  VERSION,  http=credentials.authorize(httplib2.Http()))

# List Available Reports
my_build.reportTypes().list().execute()

# That’s a 1-word dictionary, where the key is 'reportTypes' and the value is a list of dictionaries
# that contain info about each report type (keys 'id' and 'name')
results = my_build.reportTypes().list().execute()
for reportType in results['reportTypes']:
    print("Report type id: %s\n name: %s\n" % (reportType["id"], reportType["name"]))

# Ok, so let’s create a reporting job!!!!!!!!
user_activity_id = ‘channel_basic_a2’
name = ‘User activity’

reporting_job = my_build.jobs().create(
    body=dict(
      reportTypeId=user_activity_id,
      name=name
    )
  ).execute()

# You can check what jobs you have:
my_build.jobs().list().execute()

# It take a day or two for reports to begin generating.  Here's how you
# check whether the job you've created has generated any reports.
# -- Copy down a job_id
job_id = '1f8a7491-d66e-473d-xxxxxxxxxx'
my_build.jobs().reports().list(jobId=job_id).execute()

# Download Your Data!
# Each of your reports will have a unique URL associated w/ it
from apiclient.http import MediaIoBaseDownload
from io import FileIO
report_url = 'unique_report_URL' # use actual report URL! :-p
request = my_build.media().download(resourceName="")
request.uri = report_url
fh = FileIO('report', mode='wb') 
# Stream/download the report in a single request. 
downloader = MediaIoBaseDownload(fh, request, chunksize=-1)
done = False 
while done is False: 
    status, done = downloader.next_chunk() 
    if status: 
        print("Download %d%%." % int(status.progress() * 100))
print("Download Complete!")
```
