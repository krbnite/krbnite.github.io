---
layout: post
title: The YouTube Data API
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

## Data API Resources
### Channels
https://developers.google.com/youtube/v3/docs/channels/list

```python
### Channel Info
Channel ID, description, title, and more!
```python
data_api.channels().list(part='snippet', mine=True).execute()
```

### Channel Statistics
```python
data_api.channels().list(part='statistics', mine=True).execute()
```



Arguments:
* part: list of channel resources to return; if a chosen resource has a child resource, it
will also be returned
  - channel resources: contentDetails, 
* categoryID:  return all channels that match the specified YouTube guide category
* forUsername: return channel associated with given YouTube username
* hl: filter out properties that are not in a given language (used for the brandSettings part)
* id: list of channel IDs for the resources that are being retrieved
* managedByMe:  set to True if you want to return only channels owned by a given content owner ID 
  - content owner ID must be passed to the onBehalfOfContentOwnwer parameter
  - user must be an authenticated linked CMS account
* maxResults: max number of results that should be returned
* mine: set to True to only return channels associated w/ authenticated user account
  - not the same as onBehalfOfContentOwner, e.g., if I set this to True, it would return channels owned by my user account not my employer's content ID
* mySubscribers:  set to True to returns subscribers of authenticated user's channel
* onBehalfOfContentOwner: set to content owner ID if you have such permissions and have been authenticated to access all associated channels
* pageToken: identifies a specific page in the result set that should be returned
  - not currently too sure what this is...
  
```python
data_api
```



### YouTube Region Codes
An [i18nRegion](https://developers.google.com/youtube/v3/docs/i18nRegions) region code identifies a 
geographic area that a YouTube user can select as the content region. 
```python
data_api.i18nRegions().list(part='snippet').execute()
```


### YouTube Category IDs
YouTube has a classification scheme, which is dependent on region code.  For example, the first five categories
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

