---
title: Revisiting YouTube's Data API for Content Owners
layout: post
---

Let's face it: my first post on the Data API is just brain spew and chicken scratch!  
It's certaintly been a helpful reference for me as I've further played with the Reporting
and Data APIs -- but it's time for an update!  

In this short post, you will see how to paginate with the Data API and how to use the Data API on behalf of a content owner.

Specifying that you are acting on behalf of a content owner for the Data API is different than how it is
done using the Reporting API.  It took me some finagling to figure out.  For one, I needed to use a different
scope than I did in the previous post... The SSL scope.  Secondly,
the Data API demands you set two parameters to act on behalf of a content owner, not one.  In addition
to providing the content owner ID to the `onBehalfOfContentOwner` parameter, you must also set the `managedByMe` parameter
to `True`.

The additional parameter seems a little silly: if you do not set it to `True`, shit will not work.
Just imagine that type of additional security at the bank: 
* Guard: "Ok, we've authenticated you and that account number is in our system. Says here you're authorized to take money out of this accoutn...but I'm just not buying it.  I need your word!  Tell me: are you really authorized to take money from it?"
* Me: "Umm, so -- wait... You can confirm that I'm authorized to take money out, but won't unless I verbally confirm it?" 
* Guard: "Yes, I want to hear it straight from your mouth."
* Me: "I mean, I could just lie, couldn't I? Isn't that why we went through all that ID checking and verification?"
* Guard: "Don't sass me, boy! Yes or no?"
* Me: "Uh, yes. Yes, I am."
* Guard: "Good enough for me. Here's the key to the kingdom."

The interactive tool that YouTube provides was helpful (it's how I realized I needed to change the scope).
I recommend checking it out:  
* https://developers.google.com/youtube/v3/docs/channels/list#try-it

## Standard Operating Procedure: Connect to the Data API!
```python
# Load tools from Google Client API, etc
from oauth2client.client import flow_from_clientsecrets
from oauth2client.tools import run_flow
from oauth2client.file import Storage
from apiclient.discovery import build
import httplib2

#---------------------------------------------------------------
#-----------    BUILD THE DATA API CONNECTION    ---------------
#---------------------------------------------------------------
# Data API Specs
DATA_SCOPE = "https://www.googleapis.com/auth/youtube.force-ssl"
DATA_SERVICE = "youtube"
DATA_VERSION = "v3"

# Secrets and Credentials
CLIENT_SECRETS_FILE = "client_secrets.json" 
DATA_CREDENTIALS_FILE = 'data-oauth2.json'

# Authentication and Authorization
flow = flow_from_clientsecrets(CLIENT_SECRETS_FILE, scope=DATA_SCOPE,
    message="WARNING: Please configure OAuth 2.0")
data_storage = Storage(DATA_CREDENTIALS_FILE)
data_credentials = data_storage.get()    # Returns None if the file doesn't exist
if data_credentials is None or data_credentials.invalid:
  data_credentials = run_flow(flow, data_storage)

# Connect to Data API
dapi = build(DATA_SERVICE, DATA_VERSION, http=data_credentials.authorize(httplib2.Http()))
```

## Act as a Content Owner
As a content owner you don't just list your one measly channel, you list all your channels like a boss!

```python
contentOwnerId = 'str1NG0fl3tTErS2ndnUMB3rz'
dapi.channels().list(part='snippet,contentDetails,statistics',                                                                  
            managedByMe=True,                             
            onBehalfOfContentOwner=contentOwnerId).execute()
```

If you are managing a lot of channels, this will not bring you back all the data: you must paginate!
Pagination basically works like this with the Data API:
```python
contentOwnerId = 'str1NG0fl3tTErS2ndnUMB3rz'

# 1. Make 1st Request
request1 = dapi.channels().list(part='snippet,contentDetails,statistics',                                                                  
            managedByMe=True,                             
            onBehalfOfContentOwner=contentOwnerId)
response1 = request1.execute()

# 2. Use Previous Request and Response to Generate 
#      Next Request and Response
request2 = dapi.channels().list_next(request1, response1)
response2 = request2.execute()
```

You will actually want to be smart and loop it!  

```python
list_of_channels = []
request = dapi.channels().list(part='snippet,contentDetails,statistics',                                                                  
            managedByMe=True,                             
            onBehalfOfContentOwner=contentOwnerId)
response = request.execute()
list_of_channels += response['items']
while request:
  request = dapi.channels().list_next(request,response)
  if request:
    response = request.execute()
    list_of_channels += response['items']
    
# Channel Names and IDs
{item['snippet']['title']: item['id'] for item in list_of_channels}
```

