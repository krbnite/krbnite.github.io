---
title: Digging Deep with the Data API
layout: post
---

If you are a content creator/owner on YouTube, the Reporting API will provide you with a horde of data.  You just 
have to wait about two days for it.

"Ain't nobody got time for that!"

Oh, and don't forget that it only comes at a daily cadence.

<figure>
<img src="/images/wtf-clint.jpg" width="500vw">
</figure>

What if you work for a media company with 1000s of video assets, and you want to keep a damn near real-time pulse 
on all of them?  

## Attempt #1: Selenium
I first approached this problem with Selenium.  The script was set to run at a regular cadence.  It was good enough,
but not great.   Sporadically, it simply did not scrape.  Over several months, I patched and duct-taped the code together with 
`try/except` and `time.sleep()` statements, creating my own little Frankenstein.  

Still, it would occasionally fail, causing problems for other projects down the pipeline.

During this time I was doing some work with YouTube APIs as well, and gleaned that the Data API is likely able to 
replace Selenium altogether for this task.  It was just a hunch.  But it required implementation.

## Attempt #2: Data API (Search)
The Data API has a search.list method that you can restrict to a specified channel (by providing the channel ID) and
object type (which I set to 'video'). For any API call, the maximum results returned is 50.  Clearly, pagination would be
necessary to obtain tens of thousands of videos. I did everything right...except that only 400-500 videos were being
returned.  

```python
dapi = connect_to_data_api() 
list_of_videos = []
request = dapi.search().list(part='snippet',
    channelId=channelId,
    type='video',
    maxResults=50)
response = request.execute()
list_of_videos += response['items']
while request:
  request = dapi.search().list_next(request,response)
  if request:
    response = request.execute()
    list_of_videos += response['items']
# Extract desired info!
videoId = [video['id']['videoId'] for video in list_of_videos]
```

**Reason for Failure**: I found that limiting the results to ~500 is a built-in feature of Search 
([https://issuetracker.google.com/issues/35171641](https://issuetracker.google.com/issues/35171641)).

**Pro Tip**: A YouTube representative also told me that Search should be avoided whenever possible because it incurs expensive 
quota costs.  

## Attempt #3: Data API (Playlists + PlaylistItems)
In this approach, I was aware that (1) the playlists.list method could be used to return all playlist IDs
associated with a given channel ID, and (2) the playlistItems.list method could be used to return all video
IDs associated with a given playlist ID. Assuming that all videos were associated with at least one playlist,
it seemed straightforward that one can obtain all video IDs associated with the given channel ID.

This wasn't a bad assumption given that every channel has an "uploads" playlist that literally includes
all videos uploaded on the channel.

```python
# Get all playlists and their contents from a channel
def get_channel_playlists_and_videos(channelId):
    playlists = get_channel_playlists(channelId)
    num_playlists = len(playlists)
    df = pd.DataFrame(columns = ['videoId', 'videoTitle', 'publishedAt', 'playlistId'])
    for k in range(num_playlists):
        pid = playlists.playlistId[k]
        df_ = get_playlist_videos(pid)
        df = df.append(df_)
        df.reset_index(inplace=True, drop=True)
    return df
```

#### The extra bits of code
```python
def get_channel_playlists(channelId):
    list_of_playlists = []
    request = dapi.playlists().list(part='snippet',
        channelId=channelId,
        maxResults=50)
    response = request.execute()
    list_of_playlists += response['items']
    while request:
      #request = dapi.channels().list_next(request,response)
      request = dapi.playlists().list_next(request,response)
      if request:
        response = request.execute()
        list_of_playlists += response['items']
    # Flatten and Extract!
    df = pd.DataFrame(columns = ['playlistId', 'publishedAt',
        'playlistTitle', 'playlistDescription'])
    num_playlists = len(list_of_playlists)
    for idx in range(num_playlists):
        playlist = list_of_playlists[idx]
        playlistId    = playlist['id']
        publishedAt   = playlist['snippet']['publishedAt']
        playlistTitle = playlist['snippet']['title']
        playlistDescription = playlist['snippet']['description']
        df.loc[idx] = [playlistId, publishedAt, playlistTitle, playlistDescription]
    return df
```

```python
# Get playlist video IDs through .playlistItems()
def get_playlist_videos(playlistId):
    list_of_videos = []
    request = dapi.playlistItems().list(part='snippet',
        playlistId = playlistId,
        maxResults = 50)
    response = request.execute()
    list_of_videos += response['items']
    while request:
      request = dapi.playlistItems().list_next(request,response)
      if request:
        response = request.execute()
        time.sleep(1)
        list_of_videos += response['items']

    df = pd.DataFrame(columns = ['videoId', 'videoTitle', 'publishedAt', 'playlistId'])
    num_playlists = len(list_of_videos)
    for idx in range(num_playlists):
        video = list_of_videos[idx]['snippet']
        videoId = video['resourceId']['videoId']
        videoTitle = video['title']
        publishedAt   = (video['publishedAt'].
            replace('T', ' ').
            replace('.000Z', ''))
        df.loc[idx] = [videoId, videoTitle, publishedAt, playlistId]
    return df
```

**Reason for Failure**:  The playlists.list method does not return the playlist IDs of
default YouTube playlists, such as the "uploads" playlist.  Therefore, this method is only
able to return videos associated with the channel's custom playlists.

## Data API Solution: Channels + PlaylistItems
Turns out, the default YouTube playlist IDs, such as the "uploads" playlist, can be found
using the channels.list method (part='contentDetails').
