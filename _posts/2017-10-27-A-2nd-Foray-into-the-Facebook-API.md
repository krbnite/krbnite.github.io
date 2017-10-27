---
title: A 2nd Foray into the FB API
layout: post
---

In a [previous post](https://krbnite.github.io/First-Foray-Into-the-FaceBook-API/), 
I covered a little bit about accessing FaceBook's Graph API through the 
browser-based Graph Explorer tool, as well as how to access using the `facebook` Python 
module. Though the `facebook` module made things appear straightforward, it did seem to have some 
setbacks (e.g., it only supported up to version 2.7 of the API).

In this post, I continue to explore the Graph API -- with the ultimate goal of being able to 
mine some FB data using Python.  The topics are in a semi-random, stream-of-consciousness order. 

-----------------------------------------------------

## The Requests Package

The `requests` Python module is more "bare bones" than the `facebook` module.  Using it gives
a clearer picture of how to interact with the Graph API programmatically. 

This code is just to whet your appetite a bit.

```python
import requests
import json

# 1. Get Token
token = ''

# 2. Choose version
vx = 'v2.9'

# 3. Define scopes
fb_graph = 'https://graph.facebook.com/'
me = fb_graph + vx + '/me?access_token=' + token
friends = fb_graph + vx + '/me/friends?access_token=' + token
def search(q, gtype):
  return fb_graph + vx + '/search?q=' + q + '&type=' + gtype + '&access_token=' + token

# 4. Use requests GET method, requests.get()
me_data = requests.get(me)
friends_data = requests.get(friends)
search_data = requests.get(search('John Smith', 'user'))

# 5. Look at the data
json.loads(me_data.text)

```

Appetite whet?  Good, now here's the bad news: the friends scope is pretty uninteresting for v2.0 and 
after. As the Debug Message on the Graph Explorer says:
> "Only friends who installed this app are returned in API v2.0 and higher. total_count in summary 
> represents the total number of friends, including those who haven't installed the app."

Why is this bad news?  I don't know.  Seems like every article I read or video I watch is extremely 
outdated.  

## cURL the Graph from the Command Line
The following example is a two-for: it also shows how to issue [simple batch requests](https://developers.facebook.com/docs/graph-api/making-multiple-requests).

```
curl \
    -F 'access_token=...' \
    -F 'batch=[{"method":"GET", "relative_url":"me"},{"method":"GET", "relative_url":"me/friends?limit=50"}]' \
    https://graph.facebook.com
```

## Browser Graphing
Yea, you can get fancy with Schmython or Schmurl, but you can also just plug in URLs to your browser!

```
# Get some profile info (will return URL of your profile pic thumbnail)
https://graph.facebook.com/me?fields=id,name,picture&access_token=abcdefghigotagalwearshertoenailslong
```

You can select a specific Graph API version like so:
```
# Get some info on your photos
https://graph.facebook.com/v2.5/me/photos?access_token=abcdefghigotagalwearshertoenailslong
```

Fields have methods!
```
# Show comments on a photo in reverse chronological order.
https://graph.facebook.com/{photo-id}?fields=comments.order(reverse_chronological)&access_token=abcdefghigotagalwearshertoenailslong
```

--------------------------------------------------------

## Interested in Video?
That has a different endpoint:
> "Almost all requests are passed to the API at graph.facebook.com - the single exception is video 
> uploads which use graph-video.facebook.com."

---------------------------------------------------------------------------------------

## App Access Tokens
It is easy to generate an access token for yourself using the Graph Explorer, but the token expires... And
that's super inconvenient, especially if your goal it to automate some daily data mining tasks.

There is an "[Access Token Took](https://developers.facebook.com/tools/accesstoken/)" that provides some 
valuable info on tokens in general:
> The [user tokens](https://developers.facebook.com/docs/facebook-login/access-tokens/#usertokens) listed here are provided 
> for convenience to test your apps. They expire like any other user access token and should not be hard coded into your 
> apps. [App tokens](https://developers.facebook.com/docs/facebook-login/access-tokens/#apptokens) do not expire and should 
> be kept secret as they are related to your app secret. For more information on how access tokens work and should be used, see the 
> [documentation](https://developers.facebook.com/docs/facebook-login/access-tokens/). If you want to debug 
> an access token issue, try using the [access token debugger](https://developers.facebook.com/tools/debug/accesstoken/).

Basically, if you want a long-lasting token, then you want to create an app!  This means that we will need to
generate some client credentials and secrets.

Apparently, there is another method that does not require an access token ([more info here](https://developers.facebook.com/docs/facebook-login/access-tokens/#apptokens)):
> You can just pass your app id and app secret as the access_token parameter when you make a call:
> '''https://graph.facebook.com/endpoint?key=value&access_token=app_id|app_secret'''
> The choice to use a generated access token vs. this method depends on where you hide your app secret.

The downside is that an app access\_token might not necessarily have the same permissions as a user access\_token. 

## Page Access Tokens
If you are looking to track activity on a FaceBook page that you manage (or have been given permissions to operate),
then you might benefit from using the page's access\_token. 

With a [page token](https://developers.facebook.com/docs/facebook-login/access-tokens/#pagetokens):
> "you can make API calls on behalf of a Page. For example, you could post a status update to a Page 
> (rather than on the user's timeline) or read Page Insights data."

The Page Insights data is something I'd be interested in.


## Going Dark
If you want to experiment with the POST aspect of Graph API, you might consider generating an access token
with the audience set to 'Only Me'.

---------------------------------------------------------------------------------------

## Other Things to Be Aware Of (Maybe)
### The Public Feed API
There is another Facebook API called the [Public Feed API](https://developers.facebook.com/docs/public_feed/),
which is a continuous stream of public Facebook posts sent to a dedicated server over an HTTPS connection.  Seems
cool, but definitely something to look into more later on.

### Web Hooks
The Webhooks feature of the Graph API can provide real-time notifications of changes to a chosen set of topics and 
their fields:
> "For example, you could set up a webhook for the User topic and subscribe to its email field and be notified 
> whenever your users update their email addresses. This prevents you from having to rely on continuous or even 
> periodic Graph API requests just to check for updates that may or may not have happened. It also helps you avoid 
> rate limiting."

------------------------------------------------------------

## Some Textual References
* Overview: https://developers.facebook.com/docs/graph-api/overview
* Using the Graph API: https://developers.facebook.com/docs/graph-api/using-graph-api/
* Using Facebook Login for your App: https://developers.facebook.com/docs/facebook-login/
* Permissions Reference: https://developers.facebook.com/docs/facebook-login/permissions/
* Graph API Reference: https://developers.facebook.com/docs/graph-api/reference/
* Graph API Explorer: https://developers.facebook.com/tools/explorer

## Some YouTube References
* [FaceBook Data Analysis with Python](https://www.youtube.com/watch?v=LmhjVT9gIwk)

