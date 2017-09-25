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
assume you have a FaceBook account that you use post, like, and comment.  In later sections, I will assume you are acting
on behalf of a media company and are interested in tracking video metrics, but we're starting out simple.

### 1. Log in and Head to the [Graph API Explorer](https://developers.facebook.com/tools/explorer)
Or head  to the [Graph API Explorer](https://developers.facebook.com/tools/explorer), then log in -- it's
a commutative operation, so both work! 

You should see a screen like this:
<img src="/images/FB-Graph-API-Explorer.png" width="500">

### 2. Get a User Access Token
On the left-hand side of the Explorer, you should see a button that says "Get Token."  When you
click it, you will have the option to select "Get User Access Token," "Get App Access Token," and "Get Page Access Token."
Select the User Access Token.  

You should see a pop-up screen that lists an array of [permissions](https://developers.facebook.com/docs/facebook-login/permissions/)
that you might want to bestow upon this access token.  
<img src="/images/FB-Graph-API-Access-Tokens.png" width="500">

The default permission is access to your public profile -- that is, this will be your default if you do not select any other available 
permission from the list.  If you do not select any other permissions, you will not be able to do much, e.g., you will not even be
able to pull your own posts!  You also would not be able to pull your own email address, photos, or anything too interesting.  

Moral:  Check off some damn persmissions!  At least a bunch of the ones about your own user data. 
<img src="/images/FB-Graph-API-Access-Tokens-2.png" width="500">
