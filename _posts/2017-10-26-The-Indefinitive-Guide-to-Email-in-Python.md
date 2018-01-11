---
title: The Indefinitive Guide to Email in Python
layout: post
---


The function below captures bits and pieces of the things that I've learend
over the past several days.  It is usable, for sure!  But might not be ideal, e.g.,
I'm working on an object version of it that provides a little more flexibility (no
need to specify everything up front!).  Also, there seem to be better ways to 
include tables in an HTML email (e.g., pandas).  That said, here is the state
of my art at this time.  It certainly puts together a lot of pieces that
you can take or leave in your own code (attaching images, using inline images,
including data tables, etc).

```python
import smtplib
from email.mime.text import MIMEText
from email.mime.multipart import MIMEMultipart
from email.mime.image import MIMEImage
import re
import bs4
from tabulate import tabulate

def send_email(
    subject,
    body,
    sender,
    pswd,
    recipients = [],
    images = [],
    tables = [],
    attach_html=True
):

    COMMASPACE = ', '
    msg = MIMEMultipart()

    #---------------------------------------------------------
    # HEADER
    #---------------------------------------------------------
    msg['From'] = sender

    #---------------------------------------------------------
    # RECIPIENTS
    #---------------------------------------------------------
    #  -- ensure list type
    if str(type(recipients)).split("'")[1] == 'str':
        # Give benefit of doubt: single email address passed as string
        #  --- wait...can't I just accept "email1, email2, ..."?
        recipients = [recipients]
    if len(recipients) == 0:
        recipients = [sender]
    msg['To'] = COMMASPACE.join(recipients)

    #---------------------------------------------------------
    # SUBJECT 
    #---------------------------------------------------------
    msg['Subject'] = subject

    #---------------------------------------------------------
    # BODY
    #---------------------------------------------------------
    # -- if body is not in HTML, 2 copies of text are attached
    html = body
    text = bs4.BeautifulSoup(html,'lxml').text
    if len(tables) > 0:
        # HTML
        html_tables = [tabulate(tbl, headers=tbl.columns, tablefmt="html") for tbl in tables]
        html = html.format(*html_tables)
        text_tables = [tabulate(tbl, headers=tbl.columns, tablefmt="grid") for tbl in tables]
        text = text.format(*text_tables)
    msg.attach(MIMEText(html,'html'))
    if attach_html == True:
        # Provides attached copy
        msg.attach(MIMEText(html,'html'))
    msg.attach(MIMEText(text))

    #---------------------------------------------------------
    # IMAGE ATTACHMENTS
    #---------------------------------------------------------
    #  -- ensure list type
    if str(type(images)).split("'")[1] == 'str':
        # Give benefit of doubt: single email address passed as string
        images = [images]

    if len(images) > 0:
        for image in images:
            fp = open(image, 'rb')
            img= MIMEImage(fp.read())
            fp.close()
            msg.attach(img)

    #---------------------------------------------------------
    # INLINE IMAGES
    #---------------------------------------------------------
    #  -- if message is in html, check for image references and
    #     append to images list
    start=0
    while True:
        match = re.search("""img src=["']cid:""", html[start:])
        if match is not None:
            # NOTE FROM 2017-10-24:
            # For some reason this is not attaching images to attachments... No biggie, but weird.
            #  -- seems I can get it to attach, or go inline, but not both.
            pos1 = start + match.end()
            pos2 = pos1 + re.search("""["']""", html[pos1:]).start()
            filename = html[pos1:pos2].strip()
            fp = open(filename, 'rb')
            img= MIMEImage(fp.read())
            img.add_header('Content-ID', '<'+filename+'>')
            msg.attach(img)
            start = pos2
            fp.close()
        else:
            break

    #---------------------------------------------------------
    # SEND IT OUT!
    #---------------------------------------------------------
    domain = sender.split('@')[1]
    server = smtplib.SMTP('smtp.'+domain, 587)
    server.ehlo()
    server.starttls()
    server.ehlo()
    server.login(sender, pswd)
    text = msg.as_string()
    server.sendmail(sender, recipients, text)

```


## Some References
* From the Docs: [Email Examples](https://docs.python.org/2/library/email-examples.html)
* StackOverflow: [Sending Multipart html emails which contain embedded images](https://stackoverflow.com/questions/920910/sending-multipart-html-emails-which-contain-embedded-images)
* StackOverflow: [Send e-mail to Gmail with inline image using Python](https://stackoverflow.com/questions/19171742/send-e-mail-to-gmail-with-inline-image-using-python)
* ActiveState: [Send an HTML Email with Embedded Image and Plain Text Alternate](http://code.activestate.com/recipes/473810-send-an-html-email-with-embedded-image-and-plain-t/)
  - More Python Recipes from ActiveState: https://github.com/ActiveState/code
* StackOverflow: [Send table as an email body (not attachment ) in Python](https://stackoverflow.com/questions/38275467/send-table-as-an-email-body-not-attachment-in-python)
* DogDogFish: [Emailing multiple inline images in Python](http://dogdogfish.com/python-2/emailing-multiple-inline-images-in-python/)
