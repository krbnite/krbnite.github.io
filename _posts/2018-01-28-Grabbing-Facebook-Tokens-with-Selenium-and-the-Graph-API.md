---
title: Grabbing Facebook Tokens with Selenium and the Graph API
layout: post
---

I resisted using Selenium to acquire the user token.  There just has to be a way to
use requests and the Graph API, right?  I don't know.  Maybe. There are some promising
avenues, but all amounting to quite a bit of work and education!  

This past weekend, I skimmed through Facebook's Udacity course on passwordless login](https://www.udacity.com/course/passwordless-login-solutions-for-android--ud357)
with Account Kit and Facebook Login.  It definitely seems like this could be one way to do it,
but ultimately I'd have to build an Android or iOS app (following the course).  

Another route that I might still look more into is with 
[Business Manager System Users](https://developers.facebook.com/docs/marketing-api/businessmanager/systemuser):
> Make programatic, automated actions on ad objects or Pages, or do programmatic ads buying. System users 
> represent servers or software making API calls to assets owned or managed by a Business Manager. 

But this solution still requires developing an app... Can I use an app my company already has, or
would that be weird? Do I have to develop my own app? I've been learning a lot of JavaScript doing the 
#GrowWithGoogle scholarship, so I looked a bit into 
[Facebook's JavaScript SDK](https://developers.facebook.com/docs/javascript)... Purely out of interest,
I want to create an app, but dude!!! I need results now, and right now -- I know Selenium.

So just do it already!

## Getting a User Token with Selenium and BeautifulSoup
Getting the token has been my weak point in developing end-to-end automations for Facebook-related
projects.  Once I have a user token or page token, I can interact with the Graph API from Python using the requests
package. There might be a better way, but until I figure it out, Selenium works great here.


```python
# Get User Token
def get_user_token(
    username,
    password,
    driver_path = '/usr/local/share/chromedriver',
):
    browser = webdriver.Chrome(driver_path)
    GraphAPIExplorer = 'https://developers.facebook.com/tools/explorer'
    browser.get(GraphAPIExplorer)
    login = browser.find_element_by_link_text('Log In')
    login.click()
    email = browser.find_element_by_name('email')
    email.send_keys(username)
    pwrd = browser.find_element_by_name('pass')
    pwrd.send_keys(password)
    login = browser.find_element_by_name('login')
    login.click()
    page = bs4.BeautifulSoup(browser.page_source,'lxml')
    browser.quit()
    token_element = [item for item in page.select('input[type="text"]')
        if 'placeholder' in item.attrs
        and 'Access Token' in item['placeholder']]
    token = token_element[0]['value']
    return token
```

## Listing Authorized Page Tokens
You can technically grab a horde of authorized page tokens with the Selenium/BeautifulSoup script,
but its an awkward hodgepodge of regexing the shit out of it.  It's much simpler to just grab the
user token and head right to hitting the Graph API with requests.  The first thing to do, if you
need a page token, is to request a list of all the pages you are authorized to administer.

```python
def get_authorized_accounts(token):
    next_url = 'https://graph.facebook.com/me/accounts?access_token='+token
    continue_paging = True
    data = []
    while continue_paging:
        req = json.loads(
            requests.get(
                next_url
            ).text
        )
        data += req['data']
        try:
            next_url = req['paging']['next']
        except:
            continue_paging = False
```

## Choosing a Page Token
The previous function returns a list of dictionaries, which include the Facebook Page name and
Page Token (among other things).  The function below is just some shorthand for grabbing the token
you need at the moment.  

```python
def get_page_token(fb_accounts, name=''):
    if name == '':
        return [item['name'] for item in fb_accounts]
    else:
        try:
            token = [item['access_token']
                for item in fb_accounts
                if item['name'].lower() == name.lower()][0]
            return token
        except:
            return 'Invalid Account Name'
```

## Update (2018-01-30): Suspicious Activity!
When I began running these scripts on my EC2 instance, they were crashing.  So, I decided to
debug locally with a visual Selenium browser... Turned out that Facebook decided the EC2 instance
was a bad actor.  Makes sense: its requests are coming from an entirely different geographic 
region. Anyway, point is: Just log in manually and acknowledge that your EC2 instance is A-OK!
