---
layout: post
title: The YouTube Data API
tags: youtube python wwe
---

https://developers.google.com/youtube/v3/

## Client Secrets
If you will be working on behalf of a content owner, then make sure to use the appropriate Google account
when setting this up...

```python
import httplib2
from apiclient.discovery import build
from oauth2client.client import flow_from_clientsecrets
from oauth2client.file import Storage
from oauth2client.tools import run_flow

DATA_SCOPE = "https://www.googleapis.com/auth/youtube"
DATA_SERVICE = "youtube"
DATA_VERSION = "v3"

# Client Secrets File
#  -- I am using the same file I created for the Reporting API
CLIENT_SECRETS = 'client_secrets.json'

# Credentials File
CREDENTIALS_FILE = 'data-api-oauth2.json'

# OAuth2 Authentication: First Time
#  -- this will open up your browser and have you choose your Google Account
#  -- it will also have you select a channel or content owner
#  -- when completed, you will be authenticated in current session and have a JSON
#     file to use for authentication for all subsequent sessions
data_storage = Storage(CREDENTIALS_FILE)
data_flow = flow_from_clientsecrets(CLIENT_SECRETS, scope=DATA_SCOPE,  message=' Derr! ')
credentials = run_flow(data_flow, data_storage)

# OAuth2: All Subsequent Sessions
credentials = storage.get()

# OAuth2 Code (Any Session)
#  -- this code combines the above and can be used for any session
CREDENTIALS_FILE = 'data-api-oauth2.json'
data_storage = Storage(CREDENTIALS_FILE)
credentials = data_storage.get()
if credentials is None or credentials.invalid:
  data_flow = flow_from_clientsecrets(CLIENT_SECRETS, scope=DATA_SCOPE,  message=' Derr! ')
  credentials = run_flow(data_flow, data_storage)
  
# Create DATA API Connection
data_api = build(DATA_SERVICE, DATA_VERSION, http=credentials.authorize(httplib2.Http()))

```

# Data API Resources
## Activities
From the documentation:
> An activity resource contains information about an action that a particular channel, or user, has taken on YouTube. 
> The actions reported in activity feeds include rating a video, sharing a video, marking a video as a favorite, uploading a 
> video, and so forth. Each activity resource identifies the type of action, the channel associated with the action, and 
> the resource(s) associated with the action, such as the video that was rated or uploaded.


Get some info on your channel's videos
```python
info = data_api.activities().list(part='snippet,contentDetails', mine=True, maxResults=50).execute()
```
<img src=/images/data-api__activities-snippet-contentDetails.png width=400>

Only look at snippets of a particular channel:
```python
import pandas as pd
def get_channel_playlists_and_videos(channelId, publishedAfter=None, publishedBefore=None, regionCode=None):
  """
  channelId:  YouTube Channel ID
  publishedAfter, publishedBefore: YYYY-MM-DDThh:mm:ss.sZ (default: None)
  regionCode:  ISO 3166-1 alpha-2 country code
  """
  rows = pd.DataFrame(columns=['channel_id', 'channel_title', 'playlist_id', 
    'video_id', 'video_title', 'time_published'])
  temp = data_api.activities().list(
    part       = 'snippet,contentDetails', 
    maxResults = 50, 
    channelId  = channel_id,
    publishedAfter = publishedAfter,
    publishedBefore = publishedBefore
  ).execute()
  there_is_more_data = True
  offset = 0
  while there_is_more_data:
    for idx,item in enumerate(temp['items']):
      content = item['contentDetails']
      snippet = item['snippet']
      if 'upload' in content.keys():
        row = [
          snippet['channelId'], snippet['channelTitle'], None,
          content['upload']['videoId'], snippet['title'], snippet['publishedAt']
        ] #endInfo
      elif 'playlistItem' in content.keys():
        row = [
          snippet['channelId'], snippet['channelTitle'], content['playlistItem']['playlistId'],
          content['playlistItem']['resourceId']['videoId'], snippet['title'], snippet['publishedAt']
        ] #endInfo
      rows.loc[idx+offset] = row   
    try:
      temp = data_api.activities().list(
        part       ='snippet,contentDetails', 
        maxResults = 50, 
        channelId  = channel_id,
        publishedAfter = publishedAfter,
        publishedBefore = publishedBefore,
        pageToken  = temp['nextPageToken']
      ).execute()
      offset += 50
    except:
      there_is_more_data = False
  return rows

# Get Channel Info
bella_twins = 'UCIJnHb6meZoOjKF68h35QgQ' 
df = get_channel_playlists_and_videos(bella_twins)
wwe = 'UCJ5v_MCY6GNUBTO8-D3XoAg'
```

Oddly, this method seems to only be able to extract 256 per channel...?

Maybe a better way to do is this?
```python
data_api.search().list(part='snippet',channelId=wwe, type='video').execute()
```

### Reivew of `.activities` Parameters
* Required
  - Part: contentDetails, id, snippet
* Filters
  - channelId
  - mine (boolean)
* Optional
  - maxResults
  - pageToken
  - publishedAfter
  - publishedBefore
  - regionCode

Further Reading:  https://developers.google.com/youtube/v3/docs/activities/list



## Channels
https://developers.google.com/youtube/v3/docs/channels/list

### Channel Info
Channel ID, description, title, and more!
```python
data_api.channels().list(part='snippet', mine=True).execute()
```

### Channel Statistics
```python
data_api.channels().list(part='statistics', mine=True).execute()
```

### Channel Subscribers
Get a list of your subscribers, track 'em down, and do some analytics!  (If you have a lot of subscribers, you
might actually want to occasionally write them to file instead of building an extraordinarily large list.)

Here we see the `.channels` parameters `maxResults` and `pageToken` in use.
```python
maxResults = 50
temp = data_api.channels().list(part='status', mySubscribers=True, maxResults=maxResults).execute()
user_channel_id = [item['id'] for item in temp['items']]
nextPageToken = temp['nextPageToken']
totalSubs = temp['pageInfo']['totalResults']
numPages = totalSubs // maxResults +1
for i in range(1,numPages):
  temp = data_api.channels().list(part='status', mySubscribers=True, maxResults=maxResults, pageToken=nextPageToken).execute()
  user_channel_id += [item['id'] for item in temp['items']]
  nextPageToken = temp['nextPageToken']
```

Actually, after writing this, I found you get a lot of additional info by also using `part='snippet'`.  Very cool.

### Some Arguments to Be Aware Of:
* part: list of channel resources to return; if a chosen resource has a child resource, it
will also be returned
* forUsername: return channel associated with given YouTube username
* id: list of channel IDs for the resources that are being retrieved
* managedByMe:  set to True if you want to return only channels owned by a given content owner ID 
  - content owner ID must be passed to the onBehalfOfContentOwnwer parameter
  - user must be an authenticated linked CMS account
* maxResults: max number of results that should be returned
* mine: set to True to only return channels associated w/ authenticated user account
  - not the same as onBehalfOfContentOwner, e.g., if I set this to True, it would return channels owned by my user account not my employer's content ID
* mySubscribers:  set to True to returns subscribers of authenticated user's channel
* onBehalfOfContentOwner: set to content owner ID if you have such permissions and have been authenticated to access all associated channels

  
Read more about the `.channels()`: https://developers.google.com/youtube/v3/docs/channels/list


### YouTube Region Codes
An [i18nRegion](https://developers.google.com/youtube/v3/docs/i18nRegions) region code identifies a 
geographic area that a YouTube user can select as the content region. 
```python
data_api.i18nRegions().list(part='snippet').execute()
```


### YouTube Category IDs
YouTube has a [classification scheme](https://developers.google.com/youtube/v3/docs/guideCategories), 
which is dependent on region code.  For example, the first five categories
(and their IDs) used for the United States are:

* 1 - 'Film & Animation'
* 2 - 'Autos & Vehicles'
* 10 - 'Music'
* 15 - 'Pets & Animals'
* 17 - 'Sports' 

Find the rest of them using this code:
```python
us_cat_info = data_api.videoCategories().list(regionCode='US', part='snippet').execute()
[(item['id'], item['snippet']['title']) for item in us_cat_info['items']]
```


# Further Experimentation
## Captions
https://developers.google.com/youtube/v3/docs/captions

Scopes for captions are different:
* https://www.googleapis.com/auth/youtube.force-ssl
* https://www.googleapis.com/auth/youtubepartner

## Channel Banners
At the moment, I don't really care about [Channel Banners](https://developers.google.com/youtube/v3/docs/channelBanners):
> "A channelBanner resource contains the URL that you would use to set a newly uploaded image as the banner image for a channel."

## Channel Sections
https://developers.google.com/youtube/v3/docs/channelSections:
> A channelSection resource contains information about a set of videos that a channel has chosen to feature. 
> For example, a section could feature a channel's latest uploads, most popular uploads, or videos from one or 
> more playlists.

Basically just brings back info about the vertical/horizontal sections of videos: which ones are there, etc.
Not something I care to explore more at the moment.



