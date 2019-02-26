---
layout: post
title: Sending Email Notifications from the Command Line
tags: automation unix-tools python wwe
---


In a python program, I can use a try/except statement to avoid a crash.  In the case of a would-be crash,
I can save some data locally and email myself about the failure.  What if the program is supposed to log data every hour and 
something outside the try/except statement fails?  Though it may crash, we can minimize losses by having the 
program run on a schedule using cron... 

Or maybe have the program wrapped in a giant whileTrueTryExcept statement... But somehow I sense that there is still
room for single-point, fully destructive failure here.  So, let's go with cron...which itself can fail.  

All of this build up is so I can ask: How do we notify ourselves if a cron job fails?

Below are some notes.... I still haven't gotten cron itself to send an email.
-------------------------------------


#### 1. Install ssmtp
On Ubuntu:
```
sudo apt-get install ssmtp
```

#### 2. Configure the SSMTP Config File
```
sudo vim /etc/ssmtp/ssmtp.conf
```

```vi
root=your.email@gmail.com
mailhub=smtp.gmail.com:587
FromLineOverride=YES
UseSTARTTLS=YES
AuthUser=your.email@gmail.com
AuthPass=your_password
TLS_CA_File=~/cert.pem
```

#### 3. The cert.pem File
You need this file, and I'll show you how to create it, but to be honest I'm just
following along with the advice from a user on [askubuntu.com](https://askubuntu.com/questions/536766/how-to-make-crontab-email-me-with-output),
so use at your own risk and headache.  (Worked for me!)
```
cd ~  # we pointed TLS_CA_FILE at ~/cert.pem in the config file
openssl req -x509 -newkey rsa:2048 -keyout key.pem -out cert.pem -days 9999 -nodes
```

#### 4. Write an Email
In a text file:
```
To: someone.in.the.world@some_website.com
From: "Kool Aid Man" <your.email@gmail.com>
Subject: Kudos!
MIME-Version: 1.0
Content-Type: text/plain

If you're reading this, I successfully sent you an email from the command line.
```

#### 5. Test It Out
```
sendmail -i -t < test.txt 
```

Read my [source material](https://askubuntu.com/questions/536766/how-to-make-crontab-email-me-with-output) 
for more info. [Check this out](https://stackoverflow.com/questions/10175812/how-to-create-a-self-signed-certificate-with-openssl)
for more info on the openssl command.

