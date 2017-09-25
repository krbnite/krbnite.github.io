Lots of talk about YouTube lately.  What about the other places on the web a media company might host 
their content and want to better understand?

Twitter. FaceBook. Instagram. Just to name a few.

Do you point-and-click a few too many times, a few too many days a week to obtain data at any
of these sites?  Or maybe you just copy and paste what's needed on an ad hoc basis?  
What is your strategy?  What do you do when a stakeholder asks how a list of assets performed
on their first day?  Some of these sites will only give you lifetime values, not time sequences.
If you aren't collecting at a regular cadence or with a well-planned strategy, you might
unknowingly be making room for someone with a little more tech savvy to replace you!

In this post, I'll cover the bits and pieces I've learned about the FaceBook API recently.

## Exploring the Graph API
In this first section, I assume that you have never used the FaceBook Graph API.  I also
assume you have a FaceBook account that you use post, like, and comment.  In later sections (or posts,
depending on how long this one gets), I will assume you are acting
on behalf of a media company and are interested in tracking video metrics, but we're starting out simple.

### 1. Log in and Head to the [Graph API Explorer](https://developers.facebook.com/tools/explorer)
Or head  to the [Graph API Explorer](https://developers.facebook.com/tools/explorer), then log in -- it's
a commutative operation, so both work! 

You should see a screen like this:

<figure><img src="/images/FB-Graph-API-Explorer.png" width="500"></figure>

### 2. Get a User Access Token
Even to request data on yourself, you need an [access token](https://developers.facebook.com/docs/facebook-login/access-tokens).

On the left-hand side of the Explorer, you should see a button that says "Get Token."  When you
click it, you will have the option to select "Get User Access Token," "Get App Access Token," and "Get Page Access Token."
Select the User Access Token.  

You should see a pop-up screen that lists an array of [permissions](https://developers.facebook.com/docs/facebook-login/permissions/)
that you might want to bestow upon this access token.  

<figure><img src="/images/FB-Graph-API-Access-Tokens.png" width="500"></figure>

#### A Little More on Access Tokens
The type of User Access Token we generate is called a short-lived token.  It is valid for only an hour or two, then
you need to generate another one.

From FaceBook's [documentation](https://developers.facebook.com/docs/facebook-login/access-tokens#usertokens):
> User access tokens come in two forms: short-lived tokens and long-lived tokens. Short-lived tokens usually have a 
> lifetime of about an hour or two, while long-lived tokens usually have a lifetime of about 60 days. You should not 
> depend on these lifetimes remaining the same - the lifetime may change without warning or expire early. 
>
> Access tokens generated via web login are short-lived tokens, but you can 
> [convert them to long-lived tokens](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)
> by making a server-side API call along with your app secret.

We won't be covering this.  Just consider yourself warned.


### 3. Select Some Permissions
The default permission is access to your public profile -- that is, this will be your default if you do not select any other available 
permission from the list.  If you do not select any other permissions, you will not be able to do much, e.g., you will not even be
able to pull your own posts!  You also would not be able to pull your own email address, photos, or anything too interesting.  

Moral:  Check off some damn persmissions!  At least a bunch of the ones about your own user data. 

<figure><img src="/images/FB-Graph-API-Access-Tokens-2.png" width="500"></figure>

Note: my image might look different than yours (e.g., "user\_posts" in bold) because this screen is showing
me add permissions to a pre-existing access token.

### 4. GET Something About Yourself!
By default, you will see "id" and "name" fields listed in the text box. Try adding "posts" or "photos" and pressing "Submit."

<figure><img src="/images/FB-Graph-API-First-Query.png" width="500"></figure>

Very nice!  You've just issued your first GET request to the FaceBook Graph API.

Now, for your next GET request, let the Explorer tool help you.  Notice in the box on the left-hand side, there is
a list of the fields that you most recently requested.  At the bottom of this list is an option to "Search for a
field."  Click on it!  

<figure><img src="/images/FB-Graph-API-Second-Query.png" width="500"></figure>


This can make exploration so much easier when first starting out.  For example, above we selected the user\_about\_me and the
user\_posts permissions.  The field corresponding to the user\_posts permission is "posts," so one might expect that the 
field corresponding to the user\_about\_me permission is "about\_me."  

WRONG!  

The the field corresponding to the user\_about\_me permission is simply "about." Things like this are useful to know,
and can only come with practice and experimentation.  The Explorer tool definitely helps facilitate this.

You will not be able to look at fields your access token does not have permission for, and
certain fields will generate limited information based on your privacy settings.  For example, if you
try to retrieve the field `promotable_events` using the permissions we selected above, you will receive
an error message:
> "(#278) Reading advertisements requires an access token with the extended permission ads\_read"

On a last note:  For some requests, you will also notice "before" and "after" fields in the response.  These
arise because you only receive a limited amount of data per request, and to obtain more data you must ping
the API more times using this information.  This is pagination, and we saw in the various YouTube APIs as well.

### 5. GET Something About Someone Else!
With the permissions selected above, we can't really see much about another person.  That said, 
here is how you can see some info:
* Go to your friend's page
* If they have a custom user name, you will have to view the page source
* In the page source, search for "fb://profile" and copy the user id that you find
* In the Explorer, erase "me?fields=..." and replace with the user id

## From the Comfort of Python
If you're buzzing right along, your access token is probably still valid.  Given it does not expire,
you can use the same token to obtain FaceBook data from Python.  FaceBook refers to this as "token
portability":

> One important aspect to understand about access token is that they are portable. Once you have an 
> access token you can use it to make calls from a mobile client, a web browser, or from your server to Facebook's servers. 

### Python FaceBook Package
* Install it:  http://facebook-sdk.readthedocs.io/en/latest/install.html

```python
# This package only seems to support up to version 2.7 (currently on v2.10)
graph = facebook.GraphAPI(access_token="your_token", version="2.7")

# But! It works for what we need, e.g.:
my_info = graph.get_object("/me?fields=id,name,feed")
  
# Write to File
import json
with open('my_json_file.json', 'w') as f:
  json.dump(my_info, f)

# Post something
graph.put_object("me", "feed", 
  message="Testing FaceBook Graph API from the comfort of Python... 'Like' if it worked! :-p")

# Do a Search
graph.request("/search?q=wwe&type=event&limit=100")

# Get Info on an Event
events = graph.request("/search?q=wwe&type=event&limit=100")
event_0_id = events['data'][0]['id']
event_info = graph.get_object(id = event_0_id,
  fields="""attending_count,can_guests_invite,category,cover,declined_count,description,end_time,
  guest_list_enabled,interested_count,is_canceled,is_page_owned,is_viewer_admin,maybe_count,
  noreply_count,owner,parent_group,place,ticket_uri,timezone,type,updated_time""")
```

You can even go on to get a list of those "attending" the event and those that said "maybe."
Very helpful if you are the event organizer or marketer! ([More info here](https://medium.com/towards-data-science/how-to-use-facebook-graph-api-and-extract-data-using-python-1839e19d6999).)


### The Requests Package
If you find the FaceBook package limited, then go ahead and create your own
functions to interact with the FaceBook Graph API.  

[Here](https://www.sitepoint.com/2-cool-things-can-facebook-graph-api/) is a good example.

----------------------

How to generate a token from Python?

-------------------------------

Some more info:
* https://www.klipfolio.com/blog/facebook-graph-api-explorer
* https://medium.com/towards-data-science/how-to-use-facebook-graph-api-and-extract-data-using-python-1839e19d6999
