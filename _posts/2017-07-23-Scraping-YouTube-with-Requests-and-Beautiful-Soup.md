---
title: Scraping YouTube with Requests and Beautiful Soup
layout: post
---

> "The first scrape is the hardest, baby, I know."
>   -- Cheryl Crow in an Alternate Universe

Just like Cheryl Crow's sang in an alternate universe, figuring out how to scrape the
data you want from any page is always the hardest that first time.  This is because, one, you have
to explore the page source (cmd+alt+j on a Mac) to find the page elements you need to
scrape data from; and two, you often must use some string manipulation and regex techniques
to format the selected data element.  

```python
import requests
import bs4
from datetime import datetime
import pytz

# Scrape a single asset: www.youtube.com/watch?v=video_id 
def scrape_a_youtube_video(video_id):
    
    # Execute the Scrape
    youtube = 'https://www.youtube.com/watch'
    page = requests.get(youtube, params={'v': video_id})
    timestamp = datetime.now(pytz.timezone('US/Eastern'))
    bs = bs4.BeautifulSoup(page.text, 'lxml')
    
    # Extract metrics
    if bs.find('div', {'class': 'watch-view-count'}) is not None:
        views    = bs.find('div', {'class': 'watch-view-count'}).text
        likes    = bs.find('button', {'title': 'I like this'}).text
        dislikes = bs.find('button', {'title': 'I dislike this'}).text
        output = {
            'views': int(''.join(views.split(' ')[0].split(','))) \
                if views[0:2].lower() != 'no' \
                else 0,
            'likes': int(''.join(likes.split(','))) \
                if likes != '' \
                else 0,
            'dislikes': int(''.join(dislikes.split(','))) \
                if dislikes != '' \
                else 0,
            'time_scraped_est': str(timestamp).split('.')[0]
        }
    else: # Video Not Viewed!
        output = {
            'views': None, 
            'likes': None, 
            'dislikes': None,
            'time_scraped_est': str(timestamp).split('.')[0]
            
        }
    return output
```

Having figured out that first scrape, as Alternate Cheryl has said in countless interviews:

> "The second scrape? Not as bad."

Not as bad at all! You've already done all the hard work.  Generalizing from one video to a bunch is easy!

```python
import pandas as pd

# Scrape all 
def scrape_multiple_youtube_videos(vid_list):
      
    # Create DataFrame
    vid_df = pd.DataFrame(
        columns = [
            'video_id', 
            'time_scraped_est',
            'views',
            'likes',
            'dislikes',
        ]
    )
        
    # Begin Multi-Asset Scrape
    for idx in range(len(vid_list)):
        page = scrape_a_youtube_video(vid_list[idx])
        vid_df.loc[idx, 'video_id'] = vid_list[idx]
        vid_df.loc[idx, 'time_scraped_est'] = page['time_scraped_est']
        vid_df.loc[idx,'views'] = page['views']
        vid_df.loc[idx,'likes'] = page['likes']
        vid_df.loc[idx,'dislikes'] = page['dislikes']
        
    return vid_df
```

Now let's say you want to automate such a scrape every hour to keep tabs on some
list of YouTube videos.  There are probably better ways to do this (e.g., cron), but
for a quick fix, try an infinite while loop!


```python
import requests
import bs4
from datetime import datetime
import pytz
import pandas as pd
from random import choice
import time
# Some Custom Libraries
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
        hr_check = now.hour
        con      = my_redshift_tools.connect_to_redshift()

        # YouTube Scrape
        scrapes  = my_scraping_tools.scrape_youtube(latest)

        # Redshift Insertion
        try:
            _       = my_scraping_tools.update_redshift(scrapes, con, chunksize=150)
        except:
            boot_time = datetime.now(pytz.timezone('US/Eastern')).strftime('%Y%m%d-%H:%M:%S')
            filename = str(boot_time)+'.csv'
            scrapes.to_csv(filename, index=False)
            send_email('Redshift Boot!', 'We got the boot at ' + boot_time + '. The CSV file is saved in the cloud.')
        
        time.sleep(120)  # Ensure one scrape per hour
        
    else:  # Wait until top of the hour
        rand_num_of_secs = choice([i for i in range(30,90)])
        print('...try to initiate scraping again in %d seconds' % rand_num_of_secs)
        time.sleep(rand_num_of_secs)
```

If you are running that on a Linux EC2 instance, make sure to do it in a Tmux session.  This way, it will
continue running whether or not you are logged in, get disconnected, etc.
