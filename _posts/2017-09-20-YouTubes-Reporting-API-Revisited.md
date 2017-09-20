
---
layout: post
title: YouTube's Reporting API Revisited
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

## Jobs
The [jobs resource](https://developers.google.com/youtube/reporting/v1/reference/rest/v1/jobs) of
the Reporting API is your way of telling YouTube what reports you want too see at your doorstep
every morning.

The jobs resource supports 4 methods:
* You can tell YouTube, "Hey, I want this other report too," with the [create](https://developers.google.com/youtube/reporting/v1/reference/rest/v1/jobs/create) method
* You can say, "Ya know what?  I don't want that report anymore," with the [delete](https://developers.google.com/youtube/reporting/v1/reference/rest/v1/jobs/delete) method
* If you know the secret password (the jobId), you can use the 
[get](https://developers.google.com/youtube/reporting/v1/reference/rest/v1/jobs/get) method to ask, 
"Hey YouTube, tell me about that one job I have you doing?"
* If you don't remember any secret passwords, you can bully YouTube into squealing with the 
[list](https://developers.google.com/youtube/reporting/v1/reference/rest/v1/jobs/list) method

### Example: Create a New Job on Behalf of a Content Owner
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

