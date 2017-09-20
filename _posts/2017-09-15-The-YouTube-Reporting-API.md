---
layout: post
title: The YouTube Reporting API
---

## Scraping Isn't Enough
Scraping YouTube is great for getting total views, likes, dislikes, comments and sometimes a few other quantities, especially
if you want real-time estimates at better-than-daily cadence (e.g., hourly).  In terms of timeliness, scraping YouTube
for estimates of your content's viewership is better than waiting for YouTube to
officially release estimates of "valid views" in 2-3 days.  That said, you should understand that such hourly scrapes
might have some noise from the presence and removal of so-called invalid views, e.g., bots, scrapers, and page refreshes.  
This is usually a negligible difference, especially if your content generally gets a lot "valid" views (real people eyes!)
and you don't pay an offshore team to unnaturally inflate your count.

Scraping is great and satisfying to automate, but small players in the media game might be better off using
YouTube's real-time analytics if they do not frequently release too much content (I think it tracks the lateste 20 uploads)
and don't already know how to scrape.  YouTube real-time reporting is less useful if you work at a media company that releases
15-50 videos every day-- thus scraping (which I covered in several previous posts).  

Another thing to consider is whether or not timeliness is a factor: if your project doesn't need the most up-to-date
data, you can technically use YouTube analytics and get more info than you can scrape.  This is especially true if
you care about a time sequence of such data for dates gone by long before you've made a scraper.  That said, you can still 
"scrape" if selenium is in your arsenal by having it sign into your analytics.youtube.com 
account and scrape around.  

But still, is that the best way? Or just a cool way?

For example, YouTube offers the [Analytics API](https://developers.google.com/youtube/analytics/v1/data_model) for 
targeted queries against viewership and ad performance data.  If you can sign into to the CMS and do some point-and-click
to find what you seek (or seleniumize that), then you can probably use the Analytics API.  
However, the Analytics API is limited in what data it can access and how that data must be accessed. 
You might find yourself making programs with all kinds of for-loops to account for different dimensions,
metrics, and filters. This can get really nasty if you work for a content
owner with hordes YouTube channels. 

Wouldn't it be nice to just have all of the data in
your own database, which you can query however you want and bring into your favorite programming environment?

Well, that's basically what the [YouTube Reporting API](https://developers.google.com/youtube/reporting/v1/reports/) 
is for, at least in terms of your channels' viewership and ad-performance data.  For other types of data, like
what channels are associated with a given content owner ID, there exist other APIs 
(e.g., the Data API and the Content ID API).  For now, forget about that shit, lest
your head explode.  Fact is, figuring out how to use these APIs can be tricky at first, but once you figure out 
one, learning the others is simple and straightforward.

Below, I cover some basics of using the Reporting API. The [documentation](https://developers.google.com/youtube/reporting/v1/reports/) 
provides some code snippets for Python programs you can run from the bash shell, but these programs can look fairly obtuse.  
If you are like me, you would like to understand what each piece of the code 
is doing and in what sequence -- and a great way to do this is to be able to see how the code works procedurally one line at a time.  So
my goal was to dissect the various functions and do just that.  

---------------------------------------------------------

## Basic Set-Up
### 1. You Need a Google Account
There's not much to say here.  If you don't have one, you need to make one.

If you are running your own YouTube channel, then use the associated Google account.
However, if you are working for a channel or content owner, then you might need to use
a provided Google account, such as one associated with your work email address. 

My work account has been given "content owner" permissions for my employer's YouTube content. I've also
been given managerial permissions for specific channel accounts. If you are a content owner, or
are working for one, it's important to understand the difference between having managerial permissions
for a specific channel versus having the permission to "act on behalf of a content owner." (This will
be covered in a little more detail below.)

### 2. Create a Project
It doesn't need to be an app or anything. A [Google Project](https://support.google.com/cloud/answer/6158853)
is just
> "a collection of settings, credentials, and metadata about the application or applications you're 
> working on that make use of Google APIs and Google Cloud Platform resources."

To create a project, go to https://console.cloud.google.com/projectcreate.


### 3. Enable the Reporting API
We could enable a whole bunch of fun APIs -- BigQuery, Google Cloud SQL, YouTube Analytics, and more!
For now, all that matters is that you have the YouTube Reporting API enabled. 

1. Go to console.cloud.google.com
2. Select project
3. On left-side menu: APIs and services > Library 
4. Click on YouTube Reporting API
5. Click "Enable"

<figure>
    <img src="/images/google-api-library.png" width="400vw">
</figure>

See [here](https://support.google.com/cloud/answer/6158841) for more info.


### 4. Create Your Keys and Secrets
You'll also need credentials.  Google ain't just lettin' anyone in! 

1. Go to console.cloud.google.com
2. Select project
3. On left-side menu: APIs and services > Credentials
4. Click on "Create credentials"
5. Choose credential type: 
    - API key
    - OAuth client ID

You can find more info in the Google API Client's Python documentation in the 
[intro](https://developers.google.com/api-client-library/python/start/get_started)  and in the 
[authentication section](https://developers.google.com/api-client-library/python/guide/aaa_overview).
There's also this Google Console [help page](https://support.google.com/cloud/answer/6158857?hl=en&ref_topic=6262490)
about credentials, access, security, and identity.  

### 5. Create a Client Secret File
Now that you've created some credentials, you want to download the "client secrets" JSON file:
<img src=/images/google-api-credentials.png>

More Info: [Client Secrets](https://developers.google.com/api-client-library/python/guide/aaa_client_secrets)


### 5. The Google API Client for Python
```
pip install --upgrade google-api-python-client
```
Basically, if this looks confusing to you, then you have to slow down and learn a little Python.

[More info](https://developers.google.com/api-client-library/python/start/installation).



## Using the Reporting API for the First Time
What reports are available to you via the Reporting API?  How do you set up a job? 
How and when do you use all those credentials we generated above?

### Credentials
There is a relevant [code snippet](https://developers.google.com/youtube/reporting/v1/reference/rest/v1/jobs/create) 
in the Reporting API's documentation that provides a bunch
of complicated-looking functions built using the Google API.  You can basically just
use this code to create jobs...but wtf does it do?  

Having never used Google's python libraries before, it looked like pure alienese to me, 
so I distilled the code into a more procedural format to make the code easier to follow
and understand what does what (at least for someone new to the API).

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

# 
credentials_file = 'name-of-OAuth2-file.json' # e.g., projectName-oauth2.json
storage = Storage(credentials_file) 
credentials = storage.get()   # Returns None if the file doesn't exist
if credentials is None or credentials.invalid:
    credentials = run_flow(flow, storage)  # This creates the credentials_file

```


If it is your first time using the client\_secrets.json file to create the OAuth2 JSON file, 
your browser will open up to authenticate you.

#### A. Choose a Google Account 
A screen like this should pop up:
<figure>
    <img src="/images/oauth2-a.png" width="300vw"> 
</figure>

#### B. Choose an Associated YouTube Channel or Account 
The next screen should look like:
<figure>
    <img src="/images/oauth2-b.png" width="300vw"> 
</figure>

If you need to use this API on the behalf of a content owner, make sure to select the YouTube 
account that was given those permissions!  In the image above, if I choose a particular channel,
I am able to generate [channel reports](https://developers.google.com/youtube/reporting/v1/reports/channel_reports) 
for the selected channel, but not 
[content owner reports](https://developers.google.com/youtube/reporting/v1/reports/content_owner_reports) that will
include information about all other channels owned by my employer.  Only when I select my own YouTube account
can I act on behalf of the content owner and, thus, generate the more desirable content owner reports. (Without 
the permission to act on behalf of a content owner, you can only generate channel reports.)


#### C. Acceptance or Rejection 
You should be notified both in the browser and at the command line that you've been authenticated (or not):
<figure>
<img src="/images/oauth2-c.png" width="550vw"> 
</figure>


Now that the OAuth2 JSON file has been created, you won't really have to go through this rigamaroo again.
Time to build the connection to the Reporting API!


### Build the Reporting API
```python
reporting_api = build(SERVICE_NAME,  VERSION,  http=credentials.authorize(httplib2.Http()))
```
Built!  So... Now what?  What are some things you can do with the Reporting API?


### List the Reports Provided by the Reporting API 
```python
# List Available Reports
reporting_api.reportTypes().list().execute()
```
You should see a list of JSON objects, or as we call 'em in Pythonese: dictionaries. 
Since no content owner ID was passed to the `list` method, the API assumes you want
to know about which reports are available to *you* for the channel associated with
your selected YouTube account.  

<figure>
    <img src="/images/reporting-api-report-types.png" width="400vw">
</figure>

```python
# List Available Reports
contentId = 'coNt3nt0wNEr1dxyz'  # Pro Tip: Use your own content owner ID!
reporting_api.reportTypes().list(onBehalfOfContentOwner=contentId).execute()
```
<figure>
    <img src="/images/reporting-api-report-types-for-content-owner.png" width="400vw">
</figure>

```python
# That’s a 1-word dictionary, where the key is 'reportTypes' and the value is a list of dictionaries
# that contain info about each report type (keys 'id' and 'name')
results = reporting_api.reportTypes().list().execute()
for reportType in results['reportTypes']:
    print("Report type id: %s\n name: %s\n" % (reportType["id"], reportType["name"]))

# Ok, so let’s create a reporting job!!!!!!!!
user_activity_id = 'channel_basic_a2'
name = 'User Activity'

reporting_job = reporting_api.jobs().create(
    body=dict(
      reportTypeId=user_activity_id,
      name=name
    )
  ).execute()
```



### Accessing the Reports Generated by a Job
To look at what reports have been generated by a given job, I followed along with the code
snippet available [here](https://developers.google.com/youtube/reporting/v1/reference/rest/v1/jobs/list)
in the YouTube Reporting API documentation.  Again, what is useful about the code I am providing 
below is that it distills what their code is doing in a procedural way... This is important for 
interactively figuring out what each line of code does, especially if you've never used a Google API
before (like me).

```python
# You can check what jobs you have:
reporting_api.jobs().list().execute()

# It take a day or two for reports to begin generating.  Here's how you
# check whether the job you've created has generated any reports.
# -- Copy down a job_id
job_id = '1f8a7491-d66e-473d-xxxxxxxxxx'
reporting_api.jobs().reports().list(jobId=job_id).execute()

# Download Your Data!
# Each of your reports will have a unique URL associated w/ it
from apiclient.http import MediaIoBaseDownload
from io import FileIO

report_url = 'unique_report_URL' # use actual report URL! :-p
request = reporting_api.media().download(resourceName="")
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

After finagling with this code for a while and figuring this stuff out, I began looking at the Google Client API
Python documentation a bit more.  Turns out that's a good idea :-p

In the next installment, I will cover the basics of the Google library, which will then make all this 
YouTube code look a bit more readable.

## Further Reading
* [Getting Started w/ the Google API Client for Python](https://developers.google.com/api-client-library/python/start/get_started)

