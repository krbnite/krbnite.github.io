---
title: Scheduling Jobs with Cron
layout: post
---

So you've built a scraper.  Great!  But now what?  Are you going to run it every hour manually?

Frack no!  

## Eternal Python
When I made my first hourly scraper several months ago, I knew about `cron`, but I was on the go and
suffered from some mental friction towards picking up a new tool.  My solution was simple, yet complex:
I ensconced the interesting bit of code in a while loop.   

```python
from datetime import datetime
import pytz
from random import choice
import time
import my_redshift_tools
import my_scraping_tools
from my_email_tools import send_email

hr_check=25 # Initialize to impossible number
latest=[]
while True:
    now = datetime.now()
    hr = now.hour

    # If it's close enough to the top of the hour...
    if (now.minute >= 59 or now.minute <= 1) and hr != hr_check:
        print(now)
        hr_check=now.hour
        con     = my_redshift_tools.connect_to_redshift()

        # YouTube Scrape
        scrapes = my_scraping_tools.scrape_youtube(latest)

        # Redshift Insertion
        #  -- Issue #1: long-running queries often get booted between 530-730am (morning ETL hours)
        #       * SOLUTION: chunksize parameter was added to hedge against this
        #  -- Issue #2: Boots happen anyway! Sometimes during early morning ETL hours,
        #     and other times during cluster resizing episodes
        #       * SOLUTION: try/except is added to hedge against program crash
        try:
            _       = my_scraping_tools.update_redshift(scrapes, con, chunksize=150)
        except:
            boot_time = datetime.now(pytz.timezone('US/Eastern')).strftime('%Y%m%d-%H:%M:%S')
            filename = str(boot_time)+'.csv'
            scrapes.to_csv(filename, index=False)
            send_email('Redshift Boot!', 'We got the boot at ' + boot_time + '. The CSV file is saved in the cloud.')

    else: # Wait until top of the hour
        rand_num_of_secs = choice([i for i in range(30,90)])
        print('...try to initiate scraping again in %d seconds' % rand_num_of_secs)
        time.sleep(rand_num_of_secs)
```

Yes, this solution works -- it has been running for months now!  However, a major weakness is that when it fails,
it fails hard: once it crashes, no data will be collected until the program is restarted.  In the "Redshift Insertion" 
step, you can see that I added a few techniques to hedge against failure.  These tweaks have largely been successful, 
but once or twice now the program has crashed unexpectedly.  The result was an email several days later from a stakeholder:
why wasn't there any new data?

## The Mighty Cron!
Clearly, if we could just have the job scheduled to run every hour, then a crash would not be a huge deal. Sure,
maybe an hour's data is lost, but nothing more!  At the start of the next hour, a task manager can just
start the program back up.  To my mind, this makes learning about CRON and CRONTAB indispensible.

## Crontabulous!
Cron will do anything you tell it, as long as you leave a note.  That note is the crontab (CRON TABle) file,
which contains one row per cron job.  

Do you have any cron jobs scheduled currently?  Check it out:
```
crontab -l
```

If you're learning about this for the first time, chances are the list of jobs is empty.  

## Create a Cron Job
Each row in the cron tab file represents a job, which is encoded using a specific syntax:
> min hour dayOfMonth month dayOfWeek commandToBeExecuted

The parameters take on the following values:
* min: 0-59
* hour: 0-23
* dayOfMonth: 1-31
* month: 1-12
* dayOfWeek: 0-6 (Sunday=0)

As you can see, the different parameters are space delimited.  Each parameter can take a value from 
its range or a comma-separated list of values from that range, where a value may be a single number or
two hypen-separated numbers.  An asterisk may be used as a stand-in for "any legal value."

That may have sounded more complex than it really is, so check out some examples.

### Do something at 530 AM every morning
```
30 5 * * * /home/ubuntu/usrNm/doSomething.py
```

### Take out garbage at 715 PM every Tuesday and Friday night
```
15 19 * * 2,5 /home/ubuntu/familyStuff/takeOutGarbage.py
```

### Run a webscraper at the top of the hour 24/7
```
0 0-23 * * * /home/ubuntu/workStuff/scrapeTheWeb.py
```



**Pro Tip**: If you are on an AWS EC2 instance (or some other cloud service) the system time may differ than 
your local time.  If you are scheduling jobs on the EC2 instance, you must convert your local time
to the time used by the EC2 instance (likely Universal Time). It's easy to check:
```
date
# Or, for more info
timedatectl
```

## Master Class
In the python code above, I use a try/except statement to avoid a crash.  In the case of a would-be crash,
the data is saved locally and I am emailed about the failure.  What if something outside the try/except
statement fails?  What if the cron job itself fails?  

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
root=your_email@gmail.com
mailhub=smtp.gmail.com:587
FromLineOverride=YES
UseSTARTTLS=YES
AuthUser=your_email@gmail.com
AuthPass=your_password
TLS_CA_File=~/cert.pem
```

#### 3. The cert.pem File
You need this file, and I'll show you how to create it, but to be honest I'm just
following along with the advice from a user on [askubuntu.com](https://askubuntu.com/questions/536766/how-to-make-crontab-email-me-with-output),
so use at your own risk and headache.  (Worked for me!)
```
openssl req -x509 -newkey rsa:2048 -keyout key.pem -out cert.pem -days 9999 -nodes
```

